Scenario:

As a member of the Security Blue team, your assignment is to analyze a memory dump using Redline and Volatility tools. Your goal is to trace the steps taken by the attacker on the compromised machine and determine how they managed to bypass the Network Intrusion Detection System "NIDS". Your investigation will involve identifying the specific malware family employed in the attack, along with its characteristics. Additionally, your task is to identify and mitigate any traces or footprints left by the attacker.
 
Tools:
Volatility

In this scenario, we will use Tool volatility to analyze the Memory Dump

Q1- What is the name of the suspicious process?

To find the suspicious process we will use the Volatility plugin windows.malfind for finding injected code or malware in memory. 
python3 vol.py -f  /media/sf_kali_labs_data/RedLine/MemoryDump.mem  windows.malfind
![image](https://github.com/antriksh968/cyberdefenders/assets/74059350/d176c51d-8011-4666-9ea0-cf91f0605c4c)

5896    oneetx.exe
7540    smartscreen.ex

Dumped above process 5896 & 7540 from the below command 

python3 vol.py -f /media/sf_kali_labs_data/RedLine/MemoryDump.mem windows.dumpfile --pid 5896

![image](https://github.com/antriksh968/cyberdefenders/assets/74059350/2147982f-118f-4ede-9a00-b0908c91d74f)'
![image](https://github.com/antriksh968/cyberdefenders/assets/74059350/32a396a7-342e-47cc-bc72-66224f8002ea)
Checked the hash value on the Virus total and found it suspicious.
![image](https://github.com/antriksh968/cyberdefenders/assets/74059350/a8a679ab-c163-44fd-bceb-132f7be154b5)

python3 vol.py -f /media/sf_kali_labs_data/RedLine/MemoryDump.mem windows.dumpfile --pid 7540
![image](https://github.com/antriksh968/cyberdefenders/assets/74059350/331e7447-9b3d-4a63-9b13-07ab29356580)
![image](https://github.com/antriksh968/cyberdefenders/assets/74059350/0009c91b-4842-40c6-aa17-351199da9eaa)
Checked the hash value on the Virus total and found it clean.
![image](https://github.com/antriksh968/cyberdefenders/assets/74059350/dfbdeb00-e073-4a62-81c6-004e1820bbd6)

Answer: “oneetx.exe” is the malicious process

Q2- What is the child process name of the suspicious process?

python3 vol.py -f /media/sf_kali_labs_data/RedLine/MemoryDump.mem windows.pstree
We can find the name of the child process name from the above command
![image](https://github.com/antriksh968/cyberdefenders/assets/74059350/63a4d90f-3e0b-45fc-a057-846f06d2b448)

parent process name: rundll32.exe




















