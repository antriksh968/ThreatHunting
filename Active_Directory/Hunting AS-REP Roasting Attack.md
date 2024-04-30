AS-REP Roasting dumps the Kerberos hashes of user accounts that have Kerberos pre-authentication disabled. The only requirement for AS-REP to roast a user is that the user must have the pre-authentication disabled. Pre-authentication is the first step in Kerberos authentication, and is designed to prevent brute-force password guessing attacks. During the pre-authentication, the user's hash will be used to encrypt a timestamp that the domain controller will attempt to decrypt to validate that the right hash is being used and is not replaying a previous request. After validating the timestamp the KDC will then issue a TGT for the user. If pre-authentication is disabled you can request any authentication data for any user and the KDC will return an encrypted TGT that can be cracked offline because the KDC skips the step of validating that the user is really who they say they are.
This is exactly how attackers misuse the pre-authentication feature as this is the way how Active Directory works and is designed. This also makes it difficult to detect this attack as it blends in with legitimate events and activities occurring in active directory environments. However, thankfully, there are some properties which differentiates a legitimate event to that of a malicious one. We will discuss this in the detection part of the lesson. First, let’s cover how an attacker would perform AS-REP roasting.

So, to summarize this is how this attack occurs:
1-	Attacker enumerates against Active Directory user accounts that do not require pre-authentication.
2-	Attacker requests the Kerberos Ticket-Granting Ticket (TGT).
3-	The AD Domain Controller responds back with the TGT without requiring the account password as a pre-authentication.
4-	The attacker uses a tool to extract the hashes from a captured packet.



Attack
From an attacker perspective, the attacker would only need a valid domain username and should know the IP Address of the domain controller. Attackers can craft a list of usernames and use an impacket suite of python scripts. Impacket is a collection of Python classes for working with network protocols. It was made for legitimate purposes and is widely used by system administrators, penetration testers and adversaries alike. We will use “GetNPUsers.py'' script to perform this attack.
Let's assume the attacker don't know any valid account names in the domain. Attackers can use a tool like “kerbrute” which enumerates valid usernames.


Command: ./kerbrute_linux_amd64 userenum -d CYBERCONSULTING.org --dc 192.168.230.140 users.txt

![Uploading image.png…]()


 

So far what we have discussed is not part of ASREP roasting attack, that was shown just to let you know how valid account names can be enumerated by attackers.

 

Let's proceed by taking the previous example, we know 2 valid usernames so far. Let’s update our username list for valid users.

So far we don't know if any of these users have pre-authentication disabled or not. When we use "GetNPUsers.py" script from impacket and provide it a list of valid users, it will retrieve the hash of user if they have pre-authentication disabled.

Command: python3 /usr/share/doc/python3-impacket/examples/GetNPUsers.py CYBERCONSULTING.org/ -dc-ip 192.168.230.140 -user users.txt -no-pass

 

We provide the path of the python script, then the domain name which is “CYBERCONSULTING.org'', then the domain controller IP address which is “192.168.230.140”, then list of valid usernames then a "-no-pass" switch which indicates we don't have any valid credentials so far. Lets say if an attacker already has a valid set of credentials of a domain user and wants to perform lateral movement, they can use those credentials and also perform AS-REP roasting to get the user's hash and then crack it.

Lets run the command and see the output.
Command: python3 /usr/share/doc/python3-impacket/examples/GetNPUsers.py CYBERCONSULTING.org/ -dc-ip 192.168.230.140 -user users.txt -no-pass -format john

 

We can see that we got the user “abdullah” hash meaning preauthentication was disabled for this user. But it was enabled for user “cyberjunkie” that’s why we did not get any hash for that user. At the end, we specified a format flag to “john” who makes this hash crackable by “John the Ripper” tool which is a password cracking tool.
Command: john --wordlist=pass.txt hash.txt

 

Here we see the password of user “abdullah” is "Password1".
This attack can also be carried out by other tools like “Rubeus” and can be directly run from a compromised machine. The attack we carried out was from a Kali Linux machine, which shows that attackers only need access to the internal network, they don’t even need to carry out the attack from a compromised machine or necessarily require a set of credentials.

Detection
Whenever a user logs on a domain or performs any type of authentication like accessing a service etc. an event is logged in Windows Security Event logs with event ID 4768. This event is recorded whenever a Kerberos authentication ticket or in other words a Ticket Granting Ticket (TGT) is requested from the KDC which is the Domain Controller.

For example a valid user logged on to his/her workstation, an event ID of 4768 is logged along with some other events too. This makes it very difficult to detect and hunt for AS-REP roasting attacks because these tickets are requested for daily operations and is the essence of Kerberos authentication mechanism. However when a legitimate operation occurs (like a user logon to his/her workstation) the ticket has an encryption type of “0x12” which indicates that it's encrypted using AES-256. Lets see a legitimate event.

Note: All the Kerberos events are logged in the domain controller for all the users, computers in the active directory environment.

 

The above screenshot shows that the ticket encryption type is “0x12” and pre-authentication type is 2. Anything other than 0 pre-authentication type means that the user has pre-authentication enabled. Now let’s review the event from our attack.

 


Here, we see that encryption type is “0x17”. This means that ticket is encrypted using RC4 encryption algorithm. This is one of the major indicators that this attack occured, however it still leaves room for doubt as some older services in Active Directory still requires RC4 encrypted tickets. But the pre-authentication type is 0 which means it's disabled for the user and it fulfills the requirements of AS-REP roasting attack. This can greatly reduce our events and should be definitely looked into.

A SOC analyst would be looking through thousands of logs. So, to greatly reduce the noise and only look for indications of AS-REP roasting attack, analysts should use queries only to look for Event ID 4768 with the ticket encryption type of “0x17” and pre-authentication type “0”. These events should be definitely further looked into and investigated.




So to hunt for indications of AS-REP roasting accounts, analyst should look for:

1- Event ID 4768 on Domain Controller
2- Ticket Encryption Type of “0x17”
3- Pre-Authentication Type of "0"


Mitigation Steps 
1- Find any accounts in the environment which have disabled pre-authentication. This can be done by running following command from the domain controller.
Command: ‘get-aduser -f * -pr DoesNotRequirePreAuth | where {$_.DoesNotRequirePreAuth -eq $TRUE} ’
2- Enforce complex and lengthy passwords policy in the domain. This would make it difficult or impossible for attacker to crack the password of a valid user.

