# Integrating with RuleEngine—A JSON-based Rules Engine for .NET

## I. Introduction

This document aims to provide a detailed overview of [RulesEngine](https://microsoft.github.io/RulesEngine/), a JSON-based rules engine by Microsoft, which provides C# expression support and [sequence operators in LINQ](https://dynamic-linq.net/expression-language#sequence-operators).

The document is structured as follows:

1. An overview of RulesEngine, its core components, and its benefits.
2. A dive into the specific features of the RulesEngine, including workflow files, rule expressions, and parameters.
3. Practical examples tailored to our system, showing how the RulesEngine can be used in real-world scenarios.

### RulesEngine Overview

RulesEngine is open-source software built on C# and .NET, which enables the design and management of business rules. The key benefit of RulesEngine is its ability to decouple the rules and logic from the codebase. Rules can be modified, tested, and deployed independently of the main system.

### How it Works

![Block Diagram](https://github.com/jasongannon/work-samples/blob/main/images/blockDiagram.png)
The rules can be stored in any store and be fed to the system in a structure which adheres to the [schema of WorkFlow model](https://github.com/microsoft/RulesEngine/blob/main/schema/workflow-schema.json).

A wrapper needs to be created over the Rules Engine package, which will get the rules and input message(s) from storage and put it into the Engine. The wrapper then handles the output using appropriate means.

Note: For more detail, visit the [How it works section](https://github.com/microsoft/RulesEngine/wiki/Introduction#how-it-works) in [Rules Engine Wiki](https://github.com/microsoft/RulesEngine/wiki).

### Useful Links

- [RulesEngine Homepage](https://microsoft.github.io/RulesEngine/)
- [RulesEngineWiki](https://github.com/microsoft/RulesEngine/wiki)
- [RulesEngine GitHub Page](https://github.com/microsoft/RulesEngine)
- [RulesEngineEditor](https://github.com/alexreich/RulesEngineEditor): Editor for Microsoft RulesEngine - Blazor UI library
- [RulesEngineEditor Live Demo](https://alexreich.github.io/RulesEngineEditor/)

## II. RulesEngine Basics

### Workflow Files in RulesEngine

Workflow files are the main way we interact with the RulesEngine. These files define the structure and sequence of rules the RulesEngine will process.

A workflow file in RulesEngine uses a JSON structure to define a sequence of rules or workflows. Each rule within the workflow is represented as a separate block within the workflow file, and **the overall structure of the workflow file reflects the order in which the rules will be processed**.

Here's an example of a simple workflow file:

```json
{
    "WorkflowName": "SampleWorkflow",
    "Rules": [
        {
            "RuleName": "Rule1",
            "Expression": "Param1 > 100"
        },
        {
            "RuleName": "Rule2",
            "Expression": "Param2 == 'SampleValue'"
        }
    ]
}
```

In this example, SampleWorkflow consists of two rules: `Rule1` and `Rule2`. `Rule1` is an expression rule that checks if the parameter `Param1` is greater than `100`. `Rule2` is another expression rule that checks if the parameter `Param2` is equal to `'SampleValue'`.

### Defining Rules in RulesEngine

Rules in RulesEngine are primarily defined using C# expressions. These rules are declared within the workflow file and evaluated in the context of the provided input. The RulesEngine supports a wide range of C# expressions and operators, allowing us to define complex rule sets.

For example, let's assume we have a use case where a transaction amount (`transaction.amount` in our payload) must not exceed $500. We can define a rule for this in the workflow file as follows:

```json
{
    "RuleName": "TransactionLimitRule",
    "Expression": "transaction.amount <= 500"
}
```

### Parameters in RulesEngine

Parameters in RulesEngine serve as the variables used within rule expressions. They can be defined at a global or local level.

Global parameters: These parameters are available to all rules within a workflow. They are typically used for data that is required across multiple rules. Global parameters are defined at the start of a workflow file.

Local parameters: These parameters are only available within the rule block where they are defined. They are used for rule-specific data and computations.

The example below illustrates the use of both global and local parameters:

```json
{
    "WorkflowName": "SampleWorkflow",
    "GlobalParams": [
        {
            "name": "Param1",
            "value": 100
        }
    ],
    "Rules": [
        {
            "RuleName": "Rule1",
            "LocalParams": [
                {
                    "name": "LocalParam1",
                    "value": 200
                }
            ],
            "Expression": "Param1 > LocalParam1"
        }
    ]
}
```

In the workflow above, `Param1` is a global parameter, while `LocalParam1` is a local parameter for `Rule1`. The expression `Param1 > LocalParam1` compares the value of the global parameter with the local parameter.

## III. Core Features of RulesEngine

### Use of C# Expressions

RulesEngine uses C# expressions to define its rules.

C# expressions within RulesEngine can range from simple comparison operations, such as `Param1 > Param2`, to more complex expressions that incorporate C# methods and operators.

For example, if we were to check that a user's `email` parameter (in the payload of a `Create User Account` request) contains a valid email structure, we can use the following rule:

```json
{
    "RuleName": "ValidEmailRule",
    "Expression": "emailAddresses.All(email => Regex.IsMatch(email.email, '^[^@]+@[^@]+\\.[^@]+$'))"
}
```

Here we're using the `All` method from C# LINQ and the `IsMatch` method from the `Regex` class to check that all emails in the `emailAddresses` list are in a valid format.

### Setting Transaction Limit Rule

An obvious use case for the RulesEngine is enforcing limits on transaction amounts. With RulesEngine, we can easily define a rule that limits the transaction amount to a predefined maximum.

Here's an example of how we can define a rule to ensure that the `amount` parameter in the `Request Authorization of a Credit Card` payload doesn't exceed $500:

```json
{
    "RuleName": "TransactionLimitRule",
    "Expression": "details.amount <= 500"
}
```

Here, any transaction request with an amount exceeding $500 would result in the expression evaluating to `false`, indicating a rule violation.

### Setting User Notification Rule

RulesEngine can also manage user roles and notifications. For example, we can want to set a rule that sends a notification to a manager when a cashier processes a refund.

If we have a parameter `userRole` that designates the role of the user and a `refundProcessed` flag that is set to `true` when a refund is processed, the rule can look like this:

```json
{
    "RuleName": "RefundNotificationRule",
    "Expression": "refundProcessed && userRole == 'support'"
}
```

In this rule, the expression evaluates to `true` if a refund is processed (`refundProcessed is true`) and the `userRole` is `support`. Here, a notification can be triggered to alert the manager of this action.

## IV. Custom Actions in RulesEngine

### Understanding Custom Actions

Custom actions in RulesEngine allow for user-defined functionalities that can be invoked when a rule evaluates to `true` or `false`. We can create interactive and dynamic rule sets that not only validate data but also perform actions based on the validation results.

Custom actions can be anything from logging information to a console, making API calls, sending emails, and so forth. The actual implementation depends on the specific requirements of the service or application using RulesEngine.

### Implementing Custom Actions

To implement a custom action in RulesEngine, we need to define a class that implements the `ICustomAction` interface. This interface mandates a `Run` method, which is the method that RulesEngine will call when executing the custom action.

Here's an example: Suppose we want to log a message to the console when a user's credit card transaction exceeds the $500 limit we set earlier. Here's a basic implementation of this custom action in C#:

```csharp
public class LogTransactionLimitExceeded : ICustomAction
{
    public object Run(RuleParameter[] ruleParameters, RuleContext ruleContext)
    {
        Console.WriteLine("Transaction limit exceeded!");
        return null;
    }
}
```

In the above code, `LogTransactionLimitExceeded` is our custom action. When the rule it's associated with evaluates to `true`, the `Run` method is invoked, and the message `"Transaction limit exceeded!"` is logged to the console.

To associate this custom action with a rule, we need to reference it in the rule's `Actions` array:

```json
{
    "RuleName": "TransactionLimitRule",
    "Expression": "details.amount > 500",
    "Actions": [
        {
            "Action": "LogTransactionLimitExceeded",
            "Params": []
        }
    ]
}
```

With this setup, whenever a transaction exceeds the $500 limit, the `"Transaction limit exceeded!"` message will be logged to the console.

## V. Integrating RulesEngine in Our System

### Integration Process Overview

Integrating RulesEngine into the system primarily involves creating rules that align with our business logic and implementing these rules within the system's workflows. Below is a high-level view of the process:

1. **Define Our Rules**: Begin by identifying the business logic and rules that the application needs to enforce. This includes identifying the parameters that these rules will operate on.

2. **Create Workflow Files**: Once the rules are defined, they need to be written in JSON format as workflow files. These workflow files can then be loaded into RulesEngine.

3. **Implement Custom Actions**: For any action that needs to be taken when a rule evaluates to `true` or `false`, we'll need to create custom actions. These actions are implemented in C# and are linked to the rules via the workflow files.

4. **Integrate with Our Application**: Finally, integrate RulesEngine with the application. This can be as simple as creating an instance of RulesEngine in the code, loading the workflow files, and executing the rules on the data.

### Benefits of RulesEngine Integration

Integrating RulesEngine with the system offers several key benefits:

- **Flexibility and Scalability**: Rules are defined in separate workflow files, making them easy to update or modify without touching the main application code. This separation of concerns leads to a more flexible and scalable system.

- **Consistency**: By defining business logic as rules in a centralized system, we ensure consistent enforcement of business policies across all areas of the application.

- **Efficiency**: With RulesEngine, complex business rules can be executed efficiently, reducing the load on the main application and improving overall performance.

- **Maintainability**: The clear structure and syntax of RulesEngine make it easy to understand and maintain, particularly when compared to business logic embedded directly in application code.

These benefits, taken together, can contribute to smoother operations, easier maintenance, and the capability to evolve and scale the business logic as the company grows.

## VI. Demonstration

In this section, we will walk through two demonstrations to show how RulesEngine can be used to define rules and custom actions for specific use cases.

### Creating a Rule for Limiting Transaction Amount

Consider the use case where we want to set a limit on the `transaction.amount`. If the transaction amount exceeds $500, the transaction should not proceed. Below is an example of how such a rule could be defined using RulesEngine:

```json
{
  "WorkflowName": "LimitTransactionAmount",
  "Rules": [
    {
      "RuleName": "CheckTransactionAmount",
      "Expression": "input.transaction.amount <= 500",
      "ErrorMessage": "Transaction amount exceeds $500",
      "ErrorType": "Warning"
    }
  ]
}
```

This rule, named `CheckTransactionAmount`, uses the expression `input.transaction.amount <= 500` to check if the transaction amount is within the specified limit. If the rule evaluates to `false` (i.e., the transaction amount exceeds $500), an error message is returned and the rule evaluation stops.

To use this rule in the system, we would create an instance of RulesEngine, load this workflow file, and execute the rule on the data:

```csharp

var re = new RulesEngine.RulesEngine(new[] { workflow }, null);
re.ExecuteRule("LimitTransactionAmount", yourData);

```

### Defining a Custom Action for Sending a Notification

Let's now consider a different use case, where we want to send a notification to a manager whenever a refund is processed. In this case, we would need to define a custom action in addition to the rule.

Below is an example of how this could be done:

```csharp

public class NotificationAction
{
    public void NotifyManager(string message)
    {
        // code to send a notification to the manager
    }
}

```

Here, we've defined a `NotificationAction` class with a `NotifyManager` method. This method would contain the code to send a notification to the manager.

To link this custom action to a rule, we would modify our workflow file like so:

```json
{
  "WorkflowName": "RefundNotification",
  "Rules": [
    {
      "RuleName": "CheckIfRefund",
      "Expression": "input.transaction.type == 'refund'",
      "SuccessEvent": "NotifyManager"
    }
  ]
}
```

This rule, named `CheckIfRefund`, uses the expression `input.transaction.type == 'refund'` to check if the transaction type is a refund. If the rule evaluates to `true`, the `NotifyManager` success event is triggered.

To use this rule and custom action in the system, we would create an instance of RulesEngine, load this workflow file, register the `NotificationAction` class, and execute the rule on the data:

```csharp
var re = new RulesEngine.RulesEngine(new[] { workflow }, null);
re.AddOrUpdateCompiledAssembly(typeof(NotificationAction).Assembly);
re.ExecuteRule("RefundNotification", yourData);
```

In this way, RulesEngine allows us to define both rules and custom actions to handle complex business logic in a flexible and maintainable way.

## VII. Conclusion

We covered the essential components of RulesEngine including:

- The structure and function of workflow files.
- The creation and definition of rules, illustrated through real use-case examples such as setting a limit on `transaction.amount` and defining user notifications.
- The significance of parameters in RulesEngine, detailing the application of global and local rules.
- An introduction to C# expressions within RulesEngine and how they can be used to enhance rule definition.
- The concept and implementation of custom actions in RulesEngine.
- A high-level view of how RulesEngine can be integrated into the current system to boost operations and increase flexibility.

## Appendix

### Relevant C# concepts for RulesEngine development

#### Asynchronous programming (async/await)

In C#, we make a method asynchronous by adding the `async` modifier to it. This tells the compiler to generate callbacks for parts of the method body, creating a promise-style async method.

Consider the following sample code that uses the RulesEngineService to evaluate rules asynchronously:

```csharp
public async Task<IActionResult> EvaluateRules([FromBody] RuleParameters ruleParameters)
{
    try
    {
        var result = await _rulesEngineService.EvaluateAsync(ruleParameters);
        return Ok(result);
    }
    catch (Exception ex)
    {
        return BadRequest(ex.Message);
    }
}
```

In the above code, `EvaluateAsync` is a method that potentially involves I/O (network or disk) or other high-latency operations. By using await, the method can yield control back to its caller until the awaited task is complete.

#### LINQ (Language Integrated Query)

LINQ allows us to perform complex queries on collections of objects (like arrays or lists) using a SQL-like syntax. Here's an example of using LINQ to find a particular rule in a list:

```csharp
public Rule FindRule(string ruleId)
{
    var rule = _rules.Where(r => r.Id == ruleId).FirstOrDefault();
    return rule;
}
```

In this example, `_rules` is a `List<Rule>`. The LINQ query is filtering out the rule that has the same `Id` as `ruleId`.

The `List<Rule>` stores a series of `Rule` objects, where each `Rule` represents a rule defined in the workflow file.

Each `Rule` object contains the necessary information for that rule, such as the rule's identifier, the condition for the rule (expressed as a C# boolean expression), and the action to take if the rule condition is met.

When processing a payload, we iterate over this `List<Rule>` and evaluate each rule against the current payload data. Rules whose conditions are met would then have their actions executed.

#### Exception Handling

Exceptions are used in C# for handling error conditions. When an error occurs, an exception is "thrown", which stops the normal execution of the code and transfers control to an appropriate exception handler.

Here's an example of a try/catch block:

```csharp
public async Task<IActionResult> EvaluateRules([FromBody] RuleParameters ruleParameters)
{
    try
    {
        var result = await _rulesEngineService.EvaluateAsync(ruleParameters);
        return Ok(result);
    }
    catch (Exception ex)
    {
        // Log the exception details
        _logger.LogError(ex, "Error occurred while evaluating rules.");
        return StatusCode(500, "Internal server error. Please try again later.");
    }
}
```

In the above code, if an exception is thrown in the `try` block, the execution immediately transfers to the `catch` block, and the exception's message is logged and returned as part of the HTTP response.

#### Dependency Injection

Dependency Injection (DI) is a design pattern used to implement IoC (Inversion of Control). It allows us to inject objects into a class, instead of constructing them internally. ASP.NET Core supports DI out of the box.

Here's a simple example where `RulesEngineService` is injected into the controller:

```csharp
public class RulesController : ControllerBase
{
    private readonly IRulesEngineService _rulesEngineService;

    public RulesController(IRulesEngineService rulesEngineService)
    {
        _rulesEngineService = rulesEngineService;
    }
    
    //... Controller methods here ...
}
```

In the above code, the `RulesController` is dependent on the `IRulesEngineService` for its operations. The concrete implementation of `IRulesEngineService` is provided by the DI container in ASP.NET Core at runtime.

In the `Startup.cs` file, we can specify the concrete class to be used wherever `IRulesEngineService` is required:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddScoped<IRulesEngineService, RulesEngineService>();
    // ... Other services
}
```

