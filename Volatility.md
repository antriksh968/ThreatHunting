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


usage: volatility [-h] [-c CONFIG] [--parallelism [{processes,threads,off}]] [-e EXTEND] [-p PLUGIN_DIRS] [-s SYMBOL_DIRS] [-v] [-l LOG] [-o OUTPUT_DIR] [-q] [-r RENDERER] [-f FILE] [--write-config] [--save-config SAVE_CONFIG]
                  [--clear-cache] [--cache-path CACHE_PATH] [--offline] [--single-location SINGLE_LOCATION] [--stackers [STACKERS ...]] [--single-swap-locations [SINGLE_SWAP_LOCATIONS ...]]
                  plugin ...
volatility: error: argument plugin: invalid choice imageinfo (choose from banners.Banners, configwriter.ConfigWriter, frameworkinfo.FrameworkInfo, isfinfo.IsfInfo, layerwriter.LayerWriter, linux.bash.Bash, linux.capabilities.Capabilities, linux.check_afinfo.Check_afinfo, linux.check_creds.Check_creds, linux.check_idt.Check_idt, linux.check_modules.Check_modules, linux.check_syscall.Check_syscall, linux.elfs.Elfs, linux.envars.Envars, linux.envvars.Envvars, linux.iomem.IOMem, linux.keyboard_notifiers.Keyboard_notifiers, linux.kmsg.Kmsg, linux.lsmod.Lsmod, linux.lsof.Lsof, linux.malfind.Malfind, linux.mountinfo.MountInfo, linux.proc.Maps, linux.psaux.PsAux, linux.pslist.PsList, linux.psscan.PsScan, linux.pstree.PsTree, linux.sockstat.Sockstat, linux.tty_check.tty_check, linux.vmayarascan.VmaYaraScan, mac.bash.Bash, mac.check_syscall.Check_syscall, mac.check_sysctl.Check_sysctl, mac.check_trap_table.Check_trap_table, mac.ifconfig.Ifconfig, mac.kauth_listeners.Kauth_listeners, mac.kauth_scopes.Kauth_scopes, mac.kevents.Kevents, mac.list_files.List_Files, mac.lsmod.Lsmod, mac.lsof.Lsof, mac.malfind.Malfind, mac.mount.Mount, mac.netstat.Netstat, mac.proc_maps.Maps, mac.psaux.Psaux, mac.pslist.PsList, mac.pstree.PsTree, mac.socket_filters.Socket_filters, mac.timers.Timers, mac.trustedbsd.Trustedbsd, mac.vfsevents.VFSevents, timeliner.Timeliner, windows.bigpools.BigPools, windows.callbacks.Callbacks, windows.cmdline.CmdLine, windows.crashinfo.Crashinfo, windows.devicetree.DeviceTree, windows.dlllist.DllList, windows.driverirp.DriverIrp, windows.drivermodule.DriverModule, windows.driverscan.DriverScan, windows.dumpfiles.DumpFiles, windows.envars.Envars, windows.filescan.FileScan, windows.getservicesids.GetServiceSIDs, windows.getsids.GetSIDs, windows.handles.Handles, windows.info.Info, windows.joblinks.JobLinks, windows.ldrmodules.LdrModules, windows.malfind.Malfind, windows.mbrscan.MBRScan, windows.memmap.Memmap, windows.mftscan.ADS, windows.mftscan.MFTScan, windows.modscan.ModScan, windows.modules.Modules, windows.mutantscan.MutantScan, windows.netscan.NetScan, windows.netstat.NetStat, windows.poolscanner.PoolScanner, windows.privileges.Privs, windows.pslist.PsList, windows.psscan.PsScan, windows.pstree.PsTree, windows.registry.certificates.Certificates, windows.registry.hivelist.HiveList, windows.registry.hivescan.HiveScan, windows.registry.printkey.PrintKey, windows.registry.userassist.UserAssist, windows.sessions.Sessions, windows.skeleton_key_check.Skeleton_Key_Check, windows.ssdt.SSDT, windows.statistics.Statistics, windows.strings.Strings, windows.svcscan.SvcScan, windows.symlinkscan.SymlinkScan, windows.vadinfo.VadInfo, windows.vadwalk.VadWalk, windows.vadyarascan.VadYaraScan, windows.verinfo.VerInfo, windows.virtmap.VirtMap, yarascan.YaraScan)


