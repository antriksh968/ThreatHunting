A multinational corporation has been hit by a cyber attack that has led to the theft of sensitive data. The attack was carried out using a variant of the BlackEnergy v2 malware that has never been seen before. The company's security team has acquired a memory dump of the infected machine, and they want you, as a soc analyst, to analyze the dump to understand the attack scope and impact.


Q1. Which volatility profile would be best for this machine?
python2 vol.py -f /media/sf_kali_labs_data/CYBERDEF-567078-20230213-171333.raw imageinfo
![image](https://github.com/antriksh968/cyberdefenders/assets/74059350/9751dc2d-688d-4a73-b7a5-160d5f486297)

imageinfo – is the plugin command to identify information for the image like the operating system, service pack, hardware architecture, and other useful information

Q2. How many processes were running when the image was acquired?

python3 vol.py -f /media/sf_kali_labs_data/CYBERDEF-567078-20230213-171333.raw  windows.pslist

![image](https://github.com/antriksh968/cyberdefenders/assets/74059350/1e84ae12-c613-4e35-94ff-6fb430906325)
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

python3 vol.py -f /media/sf_kali_labs_data/CYBERDEF-567078-20230213-171333.raw windows.malfind 
![image](https://github.com/antriksh968/cyberdefenders/assets/74059350/ebf3d850-678f-4b9b-92b9-651f65a36e31)
Multiple processes were detected to potentially contain injected code, but I focused on analyzing svchost.exe due to the presence of the ‘MZ’ header.
Dump the process svchost.exe
svchost.exe is a legitimate system process in Windows operating systems that host multiple services in order to improve system performance and reduce resource consumption. The purpose of svchost.exe is to act as a container or “host” for various Windows services so that these services can be run more efficiently by sharing system resources.
Regarding code injection, svchost.exe has been used as a target for malware and other malicious code injection techniques. This is because svchost.exe is a legitimate process that runs with high privileges and is trusted by the operating system.

“4d 5a”: These are the first two bytes of the memory dump, which represent the “magic number” for the Windows executable file format. The values “4d 5a” are the ASCII codes for the letters “M” and “Z”, which stand for “Mark Zbikowski”, the developer who designed the original MS-DOS file format.

“90 00”: These two bytes are part of the file header and indicate the size of the executable file’s header in paragraphs. In this case, the value is 0x90, which means that the header size is 144 bytes.


python3 vol.py -f /media/sf_kali_labs_data/CYBERDEF-567078-20230213-171333.raw windows.malfind --pid 880 --dump
![image](https://github.com/antriksh968/cyberdefenders/assets/74059350/598200fc-a70b-4035-9f2a-2dbd3a92a50e)
![image](https://github.com/antriksh968/cyberdefenders/assets/74059350/0fd7d889-df6c-45d4-b7d4-bf0b03df2ffc)
![image](https://github.com/antriksh968/cyberdefenders/assets/74059350/5279645c-0689-40b7-8d17-3d152fbf5a8e)


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









