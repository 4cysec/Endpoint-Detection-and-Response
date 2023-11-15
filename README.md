<h1>Endpoint Detection and Response</h1>

 
 <h2>Description</h2>
In this Project, attacks are simulated using a virtual Windows and Linux machine and the attack is monitored, detected and blocked using the Endpoint Detection and Response agent
called LimaCharlie.Through LimaCharlie, simulated attacks are detected and blocked using detection/block rules.<br />


<h2>Languages and Utilities Used</h2>

- <b>PowerShell</b> 
- <b>Command Line</b>
- <b>Sliver</b>
- <b>Sysmon</b>
- <b>LimaCharlie</b>
- <b>Secure Shell(SSH)</b>

<h2>Environments Used </h2>

- <b>VMware Workstation Pro 17</b>
- <b>Windows </b>
- <b>Linux Ubuntu(Server Version)</b> 


<h2>Project walk-through:</h2>

The setup for the VM environment consists of many steps.  So for the detailed steps, a link is provided to Eric Capuano's <b>So you want to be a SOC Analyst?(Part1)</b> <a href=https://blog.ecapuano.com/p/so-you-want-to-be-a-soc-analyst-part>here</a>

The Summary of the VM Environment is as follows:
1.  Establishment of a Windows Virtual Machine with Windows Defender disabled/suppressed and
    and Sysmon installed. Sysmon will provide telemetry through LimaCharlie which is also installed
    on the Windows VM.

2.  Linux Virtual Machine (Ubuntu Server Version) with Sliver Command and Control framework installed.
    Sliver will be used to deliver malware to the Windows VM.

    <p align="center"><b>Attack Monitor & Detection</b><br/>



<!--
 ```diff
- text in red
+ text in green
! text in orange
# text in gray
@@ text in purple (and bold)@@
```
--!>
