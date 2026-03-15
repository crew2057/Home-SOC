
## Objective

To setup a SOC environment lab at home that would allow me to simulate attacks on Linux and Windows target machines and generate alert and ticket on the detection rule match. Further, some automated remediation actions to the endpoint device is conducted using Elastic Defend.

## Steps

- **Designing a Network diagram**
<img src="https://i.postimg.cc/BbmMW8v7/network_diagram.png" alt="SOC environment network diagram">
<p>I used draw[.]io to design this network diagram. I designed the SOC environment with Elastic for logging and OsTicket for ticketing on alerts. Fleet server was setup to manage Windows Agent and Linux Agent for forwarding logs to the elastic server. Further, A Mythic server was created which would act as a C2 Server during data exfiltration. Finally, we have Attacker's device to simulate attacks and the SOC Analyst device to respond to the alerts and tickets generated. </p>

- **Setup Elastic Server and Kibana**
<p>Deployed an Ubuntu Server in a Virtual Private Cloud and Firewall Group for security. Installed Elastic and changed the configuration in .yml file to update network host and http port. Installed Kibana and enrolled into elastic with token. Spun up Elastic and Kibana services. Finally, Added encrypted keys to kibana keystore for allowing alert generation. </p>

- **Add Windows and Linux Target devices**
<p>Deployed Windows and Linux Target devices. Installed Sysmon in Windows device to get detailed meaningful logs telemetry.</p>

- **Add Fleet Server and Install Agents in Target devices**
<p>Deployed an Ubuntu Server and installed Fleet server to that centralized host allowing it to communicate with out ELK server. Added Windows agent and Linux agent into the fleet server to log events into our Elastic. </p>

- **Setup Mythic Agent for C2 Operations**
<p>Deployed an Ubuntu Server and installed docker container before running Mythic-CLI. Once, I had access to the Mythic platform, I installed an agent and C2 profile with the intent to make C2 connection to the Windows server over port "http".  </p>

- **OsTicket Server setup and integration**
<p>Create an Apache server on windows machine to host OsTicket and updated MySQL database to store ticket information within Xampp. Used WebHook connector in Elastic to link with OsTicket using API key. </p>

### List of Machines at the end of the lab
<img src="https://i.postimg.cc/T3nDGNjn/Screenshot-2026-03-13-at-11-01-45.png" alt="SOC environment devices">

<!-- drag & drop screenshots here or use postimg.cc and reference them using imgsrc
Every screenshot should have some text explaining what the screenshot is about.
Example below.
*Ref 1: Network Diagram*
-->
