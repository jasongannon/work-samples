# Penetration Testing Compliance—Template Document

## Testing and Scanning 

This template can be used to document penetration and scanning tests relevant to system's security audit.

|Certification Name	| Certification Requirement|
|-------------------|--------------------------|
|HIPAA	            | 101.111(a)(1); 102.448(a)(5); 122.312(e)(2)| 
|GLBA	            | §222.4|
|ISO 27002	        | 17.1.1|

### 1.1	Background Information

Acme Inc. (AI) shall perform proactive testing and scanning of the AI environment to counteract the attacks which happen on a regular basis, by identifying gaps and potential weaknesses that could be exploited. Testing the environment on a regularly scheduled basis enables AI to mitigate security gaps before a breach occurs. All testing and scanning shall be performed in accordance with regulations.

### 1.2	Policy / Procedures include the following:

#### 1.2.1	

Conduct quarterly vulnerability scanning to validate integrity of the security systems and processes. External vulnerability scanning of all Internet-facing IPs must be performed by an approved scan vendor (ASV) on a quarterly basis. To maintain compliance, it is necessary to have passing scans performed by qualified personnel (meaning no medium, high, or critical vulnerabilities open) quarterly. Internal scanning may be performed by a third party. 

Both external and internal scanning will also be performed after any significant change (new components, changes in network topology, firewall rule modifications, major product upgrades). All medium, high, or critical vulnerabilities in Internal/External scans will be remediated and a scan ran again to verify remediation.

#### 1.2.2	

Conduct internal and external penetration testing to check the security of systems and processes from the aspect of an internal or external hacker. Internal and external penetration testing must be performed on an annual basis and include both application and network-layers; however, testing must be performed by a knowledgeable third-party or internal resource with penetration testing experience. 

To maintain compliance, it is necessary to have passing penetration tests (meaning no medium, high, or critical vulnerabilities) annually. In the event of any significant infrastructure or application upgrade, the internal and external penetration testing must be performed in addition to the annual test required. 

If segmentation is used to isolate the CDE from other networks, confirm scope by testing on segmentation controls at least every six months and after any changes to segmentation controls/methods. Penetration testing methodology must include: 

* Is based on industry-accepted penetration testing methods.
* Includes coverage for the entire CDW perimeter and critical systems.
* Includes testing from both inside and outside the network.
* Includes testing to validate any segmentation and scope-reduction controls.
* Define application-layer penetration tests to include, at a minimum, the vulnerabilities listed in Requirement 6.5
* Defines network-layer penetration tests to include components that support network functions as well as operating systems.
* Includes review and consideration of threats and vulnerabilities experienced in the last 12 months.
* Specifies retention of penetration testing results and remediation activities results.

#### 1.2.3	

Perform on, or require of, connected third parties with access to AI’s network these external scans on a quarterly basis. As a matter of policy, AI does not generally allow third parties access to the production cardholder system.

#### 1.2.4	

Implement process to address and mitigate any discovered vulnerabilities during testing.

#### 1.2.5	

Enforce that only AI -approved software and tools may be used to perform testing, and only authorized individuals may do so. No third-party may perform testing of any kind without express permission from AI management.

#### 1.2.6	

Perform testing during testing windows only approved by AI IT and the Compliance Officer.

#### 1.2.7	

Notify all impacted groups of the testing activities so they will be available to address any impacts on production systems.

#### 1.2.8	

Require any third parties connected to the AI network to perform regular testing and remediate vulnerabilities found in an expedient manner.

#### 1.2.9	

Retain all test findings for one year and at a minimum of 90 days on site readily available for review/audit.

#### 1.2.10	

Wireless detection of production environment is expected to be performed in a compliant manner and will include detecting and identifying any unauthorized wireless access points, including WLAN cards inserted into system components, portable or mobile devices attached to system components (USB), and wireless devices attached to network ports or devices. (See section 19.2.6 for incident response in case of detection)

