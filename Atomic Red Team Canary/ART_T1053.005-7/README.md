## Objective
Triggering alerts with Atomic Red Team (ART) and detecting the attack workflow.

## Steps
### Setup
Installed Atomic Red Team (ART) in Windows Target machine.
<pre>
powershell -exec bypass
IEX (IWR 'https://raw.githubusercontent.com/redcanaryco/invoke-atomicredteam/master/install-atomicredteam.ps1' -UseBasicParsing); 
Install-AtomicRedTeam -getAtomics
Import-Module Invoke-AtomicRedTeam
</pre>

### Attacker Scenario
Assume attacker gets into our target system and want's to get persistance into the system. One of the way he/she could use it to create an scheduled task. Here, we assume attacker created scheduled task that executes base64 encoded commands from registry.
<pre>
Invoke-AtomicTest T1053.005-7
</pre>
The above command would execute following commands by referencing yaml file in ART directory.
<pre>
reg add HKCU\SOFTWARE\ATOMIC-T1053.005 /v test /t REG_SZ /d cGluZyAxMjcuMC4wLjE= /f
schtasks.exe /Create /F /TN "ATOMIC-T1053.005" /TR "cmd /c start /min \"\" powershell.exe -Command IEX([System.Text.Encoding]::ASCII.GetString([System.Convert]::FromBase64String((Get-ItemProperty -Path HKCU:\\SOFTWARE\\ATOMIC-T1053.005).test)))" /sc daily /st #{time} 
</pre>
We can cleanup the things we have created with the above atomic with below command. It's good hygiene to perform cleanup action.
<pre>
Invoke-AtomicTest T1053.005 -Cleanup
</pre>

### Defender Actions
- First look for the execution of powershell with arguments "reg" for registry or "schtasks" for scheduled tasks.
<pre>
process.parent.name: powershell.exe and process.args: ("reg" or "schtasks")
</pre>
- Once we identify the malicious activity, we could look futher into the consecutive operations that occurred following the attacker's execution of the command by querying for parent process id.
- This query is very fragile and brings lot of false postives which would be difficult to deal with in large organization. So, we could modify the query in EQL to look at execution of both "reg" and "schtasks" within short timeframe while also checking for base64 decoding in the command.
<pre>
sequence with maxspan=8s
	[ process where process.name: "reg.exe"]
	[ process where process.name: "schtasks.exe" and stringContains(process.command_line, "FromBase64String") ]
</pre>
- This query can be added as a rule to alert on similar persistance attempt alerts.

## Lesson Learned
- Elastic Query Language (EQL) is more powerful to write query to pinpoint granular events.
- Multiple testing should be performed to craft the rule.
- Detection rules require modification based on organizational context.
