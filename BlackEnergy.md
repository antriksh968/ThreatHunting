A multinational corporation has been hit by a cyber attack that has led to the theft of sensitive data. The attack was carried out using a variant of the BlackEnergy v2 malware that has never been seen before. The company's security team has acquired a memory dump of the infected machine, and they want you, as a soc analyst, to analyze the dump to understand the attack scope and impact.


Q1. Which volatility profile would be best for this machine?

Note: In Volatility, we must choose a profile that best identifies the type of operating system and service pack that helps Volatility in identifying locations that store artifacts and useful information.

 ./volatility_2.6_lin64_standalone -f /media/sf_kali_labs_data/CYBERDEF-567078-20230213-171333.raw imageinfo
 ![image](https://github.com/antriksh968/cyberdefenders/assets/74059350/0fe6992a-fe52-48b6-82dc-07d07c38188d)

imageinfo – is the plugin command to identify information for the image like the operating system, service pack, hardware architecture, and other useful information

Q2. How many processes were running when the image was acquired?

./volatility_2.6_lin64_standalone -f /media/sf_kali_labs_data/CYBERDEF-567078-20230213-171333.raw pslist
![image](https://github.com/antriksh968/cyberdefenders/assets/74059350/e5c3b788-0e82-4c23-9c96-1b20214972b3)

From above we found 25 processes out of them 6 are not active so the current running processes are 19.

Q3. What is the process ID of cmd.exe?

python3 vol.py -f /media/sf_kali_labs_data/CYBERDEF-567078-20230213-171333.raw  windows.pslist | grep cmd.exe
![image](https://github.com/antriksh968/cyberdefenders/assets/74059350/2f571771-041b-4472-87ba-d108bd266f7d)
From the above snip we found the pid is 1960

Q4.  What is the name of the most suspicious process?
We listed the process rootkit.exe seems to be suspicious 
![image](https://github.com/antriksh968/cyberdefenders/assets/74059350/abe25ff8-bdf7-4c74-8116-486f8d28fa1a)
![image](https://github.com/antriksh968/cyberdefenders/assets/74059350/294e1730-8875-457a-8f5d-76ee4fa8491a) 
And the other indicator of being suspicious is rootkit.exe parent process is cmd.exe.

Q5. Which process shows the highest likelihood of code injection?

Note: Code injection refers to the unauthorized insertion of code into a computer program or software system. It is a common attack vector used by hackers to exploit vulnerabilities and compromise the security of a system.

/volatility_2.6_lin64_standalone -f /media/sf_kali_labs_data/CYBERDEF-567078-20230213-171333.raw malfind
![image](https://github.com/antriksh968/cyberdefenders/assets/74059350/8b19be34-4051-4f16-8bea-ecbd8721cf17)

we can see that svchost.exe is

1. not mapped to disk. (Malfind plugin will display each process that not mapped to disk.)
   
2. Protection: PAGE_EXECUTE_READWRITE
   
3. The MZ header
Now we can dump the process id 880 of svchost which we are assuming suspicious

python3 vol.py -f /media/sf_kali_labs_data/CYBERDEF-567078-20230213-171333.raw windows.dumpfile --pid 880
![image](https://github.com/antriksh968/cyberdefenders/assets/74059350/013e1bc2-23d9-454a-bab2-3f19a4cd7f91)
md5sum file.0x89a10938.0x89a10568.ImageSectionObject.svchost.exe.img
![image](https://github.com/antriksh968/cyberdefenders/assets/74059350/43d35454-ad14-40d9-acf1-5bee39029f29)
![image](https://github.com/antriksh968/cyberdefenders/assets/74059350/ab6376f1-69d3-4ddf-b909-a2baf44b4a74)
Above result from virus total seems the process is suspicious
svchost.exe



Q6. There is an odd file referenced in the recent process. Provide the full path of that file.

strings ./pid.880.vad.0x980000-0x988fff.dmp 

Q7. What is the name of the injected dll file loaded from the recent process?

DLL injection is a technique used in software development and computer security that involves inserting a dynamic link library (DLL) into the memory space of a running process. This technique can be used for a variety of purposes, including debugging, performance analysis, and malware development.

DLL injection is often used by malware to execute malicious code within a legitimate process. By injecting a malicious DLL into the memory space of a running process, malware can gain access to the resources and privileges of that process, potentially allowing it to steal data, escalate privileges, or perform other malicious actions.

There are several different methods of DLL injection, including:

Load-time injection: This involves modifying the import address table (IAT) of a process to load a malicious DLL at startup.
Run-time injection: This involves using a thread in the target process to load the malicious DLL.
Reflective injection: This involves using a DLL that is specifically designed to inject itself into a process without relying on traditional Windows API calls.

in volatility, there is a Plugin called ldrmodules it's a Plugin that can be used to detect unlinked DLLs by comparing the list of loaded modules in a process’s memory space to the list of modules in the process’s module list.

Unlinked DLLs refer to dynamic link libraries (DLLs) that are loaded into a process’s memory space, but are not listed in the process’s import address table (IAT) or module list. These DLLs are essentially “hidden” from the process and the operating system, and can be used by attackers to hide their malicious activities.









