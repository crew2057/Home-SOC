## Objective
To gain access to the target windows machine and run the payload to establish C2 connection with Mythic Server for exfiltrating confidential information.

## Steps
<img src="https://i.postimg.cc/tCm22Vwn/attack_kill_chain.png" alt="C2 exfiltration">
### Setup
Detection rule available to alert on unsuccessful logon linked with OsTicket to generate a ticket for each alert. Additional Detection rule to alert and generate ticket when real-time protection is disabled in windows defender security.

### Attacker Scenario
- Used Hydra to perform brute force attacks on the target device and get successful logon.
<br>`hydra -l Administrator -P rockyou.txt rdp://{{target_ip}}`
- After discovering credntials, connected to the target device using "freerdp" tool.
<br> `xfreerdp /v:{{target_ip}} /u:{{username}} /p:{{password}}`
- Disabled real-time protection on the target device.
- Downloaded payload generated on the Mythic server and executed it to establish C2 communication.
- Exfiltrated credential information from the target device.

### Defender Actions
- Ticket was generated which was assigned to oneself and started investigation from the time when defender real-time protection was disabled.
- Filtered for IP responsible and figured out multiple failed logon attempts before successful authentication. This was a red flag.
- Checked for command actions and found out query for "ipconfig", "whoami", "netstat" commands which points towards discovery within Mitre Tactics.
- There was execution of some suspicious file afterwards which was identified as "Apollo". Since, I only had setup endpoint telemetry and not the network telemetry, so, I was not able to see the confidential file being sent to C2 channel.
- Note: Filtering logs was much more efficient using processGUID session code which tracks particular session only.

## Lesson Learned
- Close and disallow common ports and protocols which are not in use.
- Make use of strong password. Nowadays, the more secure way for authentication includs MFAs, passphrase or passkeys.
- Setting up firewall rules for blocking outbound connection attempting to send large file size.
- In real-world scenarios, victims usually fall into traps downloading payload to their devices. Those payload are crafted to run even without the interaction of the user to perform various activities among which establishing C2 channel can be the one. 
