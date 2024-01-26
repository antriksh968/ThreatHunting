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







