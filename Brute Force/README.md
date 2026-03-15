## Objective
To simulate brute force attacks on Windows machine and Linux machine and analyze alerts in the Elastic SIEM. Performed login attempts through RDP (3389) protocol for Windows device  and SSH (22) protocol for accessing Linux device.

## Steps
### Setup
Added detection rules in Elastic to alert on failed sign-in attempt for "Administrator" user through RDP and "root" user through ssh.

### Attacker Scenario
Used Hydra to perform brute force attacks on the target devices.
<br>_Linux Target:_ `hydra -l root -P rockyou.txt ssh://{{host_ip}}`
<br>_Windows Target:_ `hydra -l Administrator -P rockyou.txt rdp://{{host_ip}}`

### Defender Actions
- Looked for alert section within Defender to view all the brute force attempts to our target devices.
- Investigated if there have been any successful logon from unknown IPs to our target devices using Event code "4624" for windows machine and "Accepted" logon for linux machine.
- Cross-referenced with third-party tools like "abuseipdb" and "greynoise" to find if the IPs were seen attempting unauthorized access on other devices as well.
- Found couple of logons from unknown IPs and took action to block those IPs using firewall rules.
- Changed Password of the authenticated devices.

## Lesson Learned
- Close and disallow common ports and protocols which are not in use.
- Make use of strong password. Nowadays, the more secure way for authentication includs MFAs, passphrase or passkeys.
