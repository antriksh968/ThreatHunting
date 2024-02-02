Core windows process

![image](https://github.com/antriksh968/cyberdefenders/assets/74059350/d748a96c-7a80-4e10-af29-cbde1e12230e)


The hierarchy of Windows core processes can be represented as follows:

System: This is the first Windows process that runs on the system and has a PID of 4. It is responsible for managing hardware abstraction, device drivers, kernel threads, and system resources1. It is the parent process of smss.exe.

smss.exe: This is the Session Manager Subsystem that creates user sessions and starts other processes such as csrss.exe and winlogon.exe. It self-terminates after its job is done2. It is the child process of System and the parent process of csrss.exe and wininit.exe.

csrss.exe: This is the Client Server Runtime Process that handles graphical user interface and console functions. It runs in two instances: one for the system session and one for the user session2. It is the child process of smss.exe and the parent process of conhost.exe and dwm.exe.

wininit.exe: This is the Windows Initialization Process that starts the Windows subsystem and launches services.exe and lsass.exe. It runs in the system session3. It is the child process of smss.exe and the parent process of services.exe and lsass.exe.

services.exe: This is the Service Control Manager that manages the loading and unloading of Windows services. It runs in the system session and spawns multiple instances of svchost.exe to host different services4. It is the child process of wininit.exe and the parent process of svchost.exe.

svchost.exe: This is the Service Host that hosts one or more Windows services in a shared process. It runs with different parameters and privileges depending on the service group it belongs to5. It is the child process of services.exe and the parent process of various services.

winlogon.exe: This is the Windows Logon Application that handles user logon and logoff, secure attention sequence, and user profile loading. It runs in the user session and launches userinit.exe and explorer.exe. It is the child process of smss.exe and the parent process of userinit.exe and explorer.exe.

lsass.exe: This is the Local Security Authority Subsystem Service that enforces security policies, generates security tokens, and handles authentication and authorization. It runs in the system session and may spawn lsaiso.exe if Credential Guard is enabled. It is the child process of wininit.exe and the parent process of lsaiso.exe.

userinit.exe: This is the User Initialization Process that runs user scripts and initializes the user environment. It runs in the user session and terminates after launching explorer.exe. It is the child process of winlogon.exe and the parent process of explorer.exe.

explorer.exe: This is the Windows Explorer that provides the graphical shell, desktop, taskbar, and file manager. It runs in the user session and can be restarted if needed. It is the child process of userinit.exe and the parent process of various applications.

This is a simplified overview of the Windows core process hierarchy.
