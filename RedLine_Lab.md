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


Q3- What is the memory protection applied to the suspicious process memory region?

![image](https://github.com/antriksh968/cyberdefenders/assets/74059350/78236915-afda-4281-823c-8addecdd75a8)
PAGE_EXECUTE_READWRITE

Q4- What is the name of the process responsible for the VPN connection?
 python3 vol.py -f /media/sf_kali_labs_data/RedLine/MemoryDump.mem windows.pslist
![image](https://github.com/antriksh968/cyberdefenders/assets/74059350/be202a23-c412-41f5-9935-f5f6a7045716)

Now going back to the process tree, we can see a process called tun2socks.exe, which sounds like a process to do something with SOCKS which is a protocol for creating proxy connections and if we observe in the network listing,

tun2socks.exe is actually a component of some VPN solutions, specifically those that utilize the TUN/TAP driver. It is responsible for redirecting network traffic from the VPN interface to a SOCKS proxy server,
python3 vol.py -f /media/sf_kali_labs_data/RedLine/MemoryDump.mem windows.pslist | grep -E "4628|6724"
![image](https://github.com/antriksh968/cyberdefenders/assets/74059350/6597e79b-5ef2-4f3f-a4b3-03ed66206f32)

we can see here the tun2socks.exe is a child process for Outline.exe
so the basic process which is responsible about VPN is — -> “Outline.exe”

Q5- What is the attacker's IP address?

If we want to know more about the network info and display network connections, including open ports, IP addresses, and associated processes, we will use the “netscan” plugin
python3 vol.py -f /media/sf_kali_labs_data/RedLine/MemoryDump.mem windows.netscan | grep -E "5896"
![image](https://github.com/antriksh968/cyberdefenders/assets/74059350/6b0ec66a-24f8-43b1-b764-339f3c07d1c5)




















