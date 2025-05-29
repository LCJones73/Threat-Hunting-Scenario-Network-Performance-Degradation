# Threat Hunting Scenario Network Performance Degradation

## It's a busy morning at CypherT3ch and the server team is catching wind that there is what appears to be network degredation

## 10:15 AM – Trouble Brews<BR>
It’s a busy Wednesday at a financial services company. Systems are stable, patching was completed overnight, and the server team is catching up on backlogs. The server team notices an unusual submission of help desk tickets on end user system performance being unusually slow. The first question the server team is trying to tackle is if the network is under attack.

## 11:00 AM - The Call<BR>
After some investigation, they notice there is significant network performance degregation. Specifically with older devices on the 10.0.0.0/16 Network. The server team reaches out to the Security Operations Center (SOC) to get some help identifying if there is an attack happening.

Now that the stage is set, let's work through this scenario and see what we as the SOC Analyst discovers.

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

VM-MDE-Test-CJ was found failing several Connection Requests (Total = 23) against other hosts on the same network:

![InitialCode](https://github.com/user-attachments/assets/0a2f2dc5-d91d-47bd-bb13-6c048acf93aa)

The following results show what the above KQL code was able to discover:<BR><BR>

![Initial Findings](https://github.com/user-attachments/assets/53db6895-d85b-4f19-9438-87470aca1d9f)

Doing a scan on the suspected LocalIP 10.1.0.19, I can see what’s happening:<BR><BR>

![Secondary Code](https://github.com/user-attachments/assets/686be94d-eab1-4547-9f52-2af22176f8bd)

This search discovered the following results. These indicate this was a port scan. A port scanner is an application designed to probe a server or host for open ports:<BR><BR>

![Secondary Findings](https://github.com/user-attachments/assets/63ef13ce-f7ce-44fb-979a-564f4e9cb0c4)

You can see the LocalIP is scanning the RemoteIP and this is an obvious port scan as you observe the well known RemotePorts are scanned in sequential order starting with “8443”.

![Deeper Code](https://github.com/user-attachments/assets/e4834ce4-b537-42c9-851e-6322a629925d)

Ran the KQL code above specifically looking for a Timestamp, FileName and InitiatingProcessCommandLine and see what I can observe and discover:

![Deeper Findings](https://github.com/user-attachments/assets/fbc3887a-f50c-4841-921f-abe23eb8fd3c)

I noticed a powershell.exe script was launched, this script was called “portscan.ps1” at 2025-05-09T02:46:39.2819461Z. I logged into the suspect computer and observed the powershell script that was used to conduct the port scan.

![Powershell Script](https://github.com/user-attachments/assets/0dcaa81f-7441-4ec0-8c18-5c15770d56e7)

Going a step further to simulate a real event, I investigated who ran the script by adding AccountName after the InitiatingProcessCommandLine which gave the following results:

![Final Findings](https://github.com/user-attachments/assets/943f4759-bb57-42e9-a2f4-a4c3def1ba12)

You could confront the user and if they say they did not run the script, or know what you’re talking about. We can assume their device is compromised by Malware. So, we would need to quarantine the computer and bring it offline until remediation can be completed.

Remediation Steps:
In Microsoft Defender, we would go to “Device Inventory” 
Search our compromised computer, in this case “VM-MDE-Test-CJ” 
We would then click on the 3 dots at the top right
Select “Run Antivirus Scan”
Select “Isolate Device”
Put in a ticket to have it re-imaged/rebuilt

There is more that can be done and in a real world we would use the DeviceFileEvents table to see if we can discover where the “portscan.ps1” file came from, or when it was created.You can also cross reference with the MITRE ATT&CK Framework to discover TTPs.

Example:
T1059.001: Command and Scripting Interpreter: PowerShell
T1040: Network Sniffing
T1046: Network Service Scanning
T1075: Pass the Hash (Potential Lateral Movement)

<p align="center">
  <strong>This wraps up Threat Hunting Scenario: Network Performance Degradation</strong>
</p>

<p align="center">
____________________________________________________________________________________________________________________________
</p><BR>
<p align="center">
  <img src="https://github.com/user-attachments/assets/357dc7ff-c6d7-4d02-9200-97c028e0a2a7" alt="Description" width="400"/>
</p>
<p align="center">
____________________________________________________________________________________________________________________________
</p>
