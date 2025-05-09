# Threat Hunting Scenario Network Performance Degradation
The server team has noticed a significant network performance degradation on some of their older devices attached to the network.

### - Preparation
> [!NOTE]
> <strong>Set up the hunt by defining what you're looking for:</strong><BR><BR>
The server team has noticed a significant network performance degradation on some of their older devices attached to the network in the 10.0.0.0/16 network. After ruling out external DDoS attacks, the security team suspects something might be going on internally.<BR><BR>
Activity: All traffic originating from within the local network is by default allowed by all hosts. There is also unrestricted use of PowerShell and other applications in the environment. It’s possible someone is either downloading large files or doing some kind of port scanning against hosts in the local network.
### - Data Collection
> [!TIP]
> <strong>Gather relevant data from logs, network traffic, and endpoints.</strong><BR><BR>
Consider inspecting the logs for excessive successful/failed connections from any devices.  If discovered, pivot and inspect those devices for any suspicious file or process events.<BR><BR>
Activity: Ensure data is available from all key sources for analysis.
Ensure the relevant tables contain recent logs:<br><br>
DeviceNetworkEvents<BR>
DeviceFileEvents<BR>
DeviceProcessEvents

### - Data Analysis
> [!TIP]
> <strong>Analyze data to test your hypothesis.</strong><BR><BR>
Activity: Look for anomalies, patterns, or indicators of compromise (IOCs) using various tools and techniques.
Anything going on that you can notice in terms of excessive network connections to/from any hosts? Take note of the the query/logs/time
### - Investigation
> [!WARNING]
> <strong>Investigate any suspicious findings.</strong><BR><BR>
Activity: Dig deeper into detected threats, determine their scope, and escalate if necessary. See if anything you find matches TTPs within the MITRE ATT&CK Framework.
Search the DeviceFileEvents and DeviceProcessEvents tables around the same time based on your findings in the DeviceNetworkEvents tables to see if you can find more evidence for the cause of network slowdowns.
### - Response
> [!WARNING]
> <strong>Mitigate any confirmed threats.</strong><BR><BR>
Activity: Work with security teams to contain, remove, and recover from the threat.
Can anything be done?
### - Documentation
> [!NOTE]
> <strong>Record your findings and learn from them.</strong><BR><BR>
Activity: Document what you found and use it to improve future hunts and defenses.
Document what you did
### - Improvement
> [!IMPORTANT]
> <strong>Improve your security posture or refine your methods for the next hunt.</strong><BR><BR>
Activity: Adjust strategies and tools based on what worked or didn’t.
Anything we could have done to prevent the thing we hunted for? Any way we could have improved our hunting process?

## Notes / Findings:

### Timeline Summary and Findings:

VM-MDE-Test-CJ has been internet facing for several days

DeviceInfo<BR>
| where DeviceName == "vm-mde-test-cj"<BR>
| where IsInternetFacing == true<BR>
| order by Timestamp desc<BR>

Last interfacing time: 2025-05-02T01:00:39.654423Z___

Using KQL to check for any bad actors that may have been attempting to log into the target network:<BR><BR>

<p align="center">
  <img src="https://github.com/user-attachments/assets/119f2014-ba96-4bc0-b010-4cae58452298" alt="Description" width="800"/>
</p><BR>

The following results show that none of the attempted logins were suspicious login attempts after exploring the TTP of the MITRE ATT&CK Framework. It is clear from my investigation that no <strong>T1110 Brute Force (MITRE ATT&CK Framework)</strong> logins were attempted.<BR><BR>

<p align="center">
  <img src="https://github.com/user-attachments/assets/f500c383-c0a5-4113-a0e0-02ff01d4279c" alt="Description" width="800"/>
</p><BR><BR>

<p align="center">
  <strong>That wraps up Threat Hunting Scenario: Investigating Internet Facing VM's</strong>
</p>

<p align="center">
____________________________________________________________________________________________________________________________
</p><BR>
<p align="center">
  <img src="https://github.com/user-attachments/assets/1d130215-59d9-4e38-978c-87f024ce3604" alt="Description" width="400"/>
</p>
<p align="center">
____________________________________________________________________________________________________________________________
</p>
