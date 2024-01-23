Volatility CheatSheet:

OS Information
#vol3 has a plugin to give OS information (note that image info from vol2 will give you OS info)
./vol.py -f file.dmp windows.info.Info


Hashes/Passwords
Extract SAM hashes, domain cached credentials and lsa secrets.
./vol.py -f file.dmp windows.hashdump.Hashdump #Grab common windows hashes (SAM+SYSTEM)
./vol.py -f file.dmp windows.cachedump.Cachedump #Grab domain cache hashes inside the registry
./vol.py -f file.dmp windows.lsadump.Lsadump #Grab lsa secrets


Memory Dump
The memory dump of a process will extract everything of the current status of the process. The procdump module will only extract the code.
volatility -f file.dmp --profile=Win7SP1x86 memdump -p 2168 -D conhost/



Processes
List processes
Try to find suspicious processes (by name) or unexpected child processes (for example a cmd.exe as a child of iexplorer.exe).
It could be interesting to compare the result of pslist with the one of psscan to identify hidden processes.
python3 vol.py -f file.dmp windows.pstree.PsTree # Get processes tree (not hidden)
python3 vol.py -f file.dmp windows.pslist.PsList # Get process list (EPROCESS)
python3 vol.py -f file.dmp windows.psscan.PsScan # Get hidden process list(malware)



Dump proc
./vol.py -f file.dmp windows.dumpfiles.DumpFiles --pid <pid> #Dump the .exe and dlls of the process in the current directory



Command line
Anything suspicious was executed?
python3 vol.py -f file.dmp windows.cmdline.CmdLine #Display process command-line arguments

Commands entered into cmd.exe are processed by conhost.exe (csrss.exe prior to Windows 7). So even if an attacker managed to kill the cmd.exe prior to us obtaining a memory dump, there is still a good chance of recovering history of the command line session from conhost.exe’s memory. If you find something weird (using the console's modules), try to dump the memory of the conhost.exe associated process and search for strings inside it to extract the command lines.



Environment
Get the env variables of each running process. There could be some interesting values.
python3 vol.py -f file.dmp windows.envars.Envars [--pid <pid>] #Display process environment variables


SIDs
Check each SSID owned by a process.
It could be interesting to list the processes using a privileges SID (and the processes using some service SID).
./vol.py -f file.dmp windows.getsids.GetSIDs [--pid <pid>] #Get SIDs of processes
./vol.py -f file.dmp windows.getservicesids.GetServiceSIDs #Get the SID of services
volatility --profile=Win7SP1x86_23418 getsids -f file.dmp #Get the SID owned by each process
volatility --profile=Win7SP1x86_23418 getservicesids -f file.dmp #Get the SID of each service

Handles
Useful to know to which other files, keys, threads, processes... a process has a handle for (has opened)
vol.py -f file.dmp windows.handles.Handles [--pid <pid>]
volatility --profile=Win7SP1x86_23418 -f file.dmp handles [--pid=<pid>]


DLLs
./vol.py -f file.dmp windows.dlllist.DllList [--pid <pid>] #List dlls used by each
./vol.py -f file.dmp windows.dumpfiles.DumpFiles --pid <pid> #Dump the .exe and dlls of the process in the current directory process
volatility --profile=Win7SP1x86_23418 dlllist --pid=3152 -f file.dmp #Get dlls of a proc
volatility --profile=Win7SP1x86_23418 dlldump --pid=3152 --dump-dir=. -f file.dmp #Dump dlls of a proc



Services
./vol.py -f file.dmp windows.svcscan.SvcScan #List services
./vol.py -f file.dmp windows.getservicesids.GetServiceSIDs #Get the SID of services



Network
./vol.py -f file.dmp windows.netscan.NetScan


Registry hive
Print available hives
./vol.py -f file.dmp windows.registry.hivelist.HiveList #List roots
./vol.py -f file.dmp windows.registry.printkey.PrintKey #List roots and get initial subkeys


Get a value
./vol.py -f file.dmp windows.registry.printkey.PrintKey --key "Software\Microsoft\Windows NT\CurrentVersion"


