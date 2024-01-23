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






