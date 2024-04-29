**Active Directory (AD) **is a directory service developed by Microsoft for Windows domain networks. It is a centralized database that stores information about resources on a network, such as users, computers, and applications. AD is used to manage and control access to network resources, such as files, printers, and other shared resources.AD is an important component of many enterprise networks and is widely used in organizations of all sizes. It integrates with other Microsoft technologies, such as Exchange and SharePoint, to provide a comprehensive solution for managing user authentication, authorization, and access to resources on the network. Additionally, it supports the use of Group Policy Objects (GPOs) to enforce security and compliance policies, as well as provide an infrastructure for software distribution and patch management.
The main core of Active Directory is Kerberos. It is a protocol used for authentication, authorization process for large scale environments. We will cover basic terms in Active Directory as well as how Kerberos authentication works

**What is Kerberos?**

Kerberos is the default authentication service for Microsoft Windows domains. It is intended to be more "secure" than NTLM by using third party ticket authorization as well as stronger encryption. It runs on UDP port 88.

Kerberos is a network authentication protocol designed to provide strong authentication for client/server applications by using secret-key cryptography. It allows entities communicating over a non-secure network to prove their identity to each other securely. The Kerberos protocol relies on a trusted third party, known as the Key Distribution Center (KDC), to facilitate authentication.

Let's walk through a simple example to understand how Kerberos works:

**Initialization**:
Alice wants to access a network resource, such as a file server, from her computer.
Bob is the server hosting the resource that Alice wants to access.
There's also a trusted third party, the Key Distribution Center (KDC), responsible for authentication.

**Authentication Process**:

**Step 1**: Authentication Request: Alice sends an authentication request to the KDC, requesting access to the file server hosted by Bob. 
This request includes:
Alice's identity (principal)
The name of the file server (Bob)
A timestamp

**Step 2**: Ticket Granting Ticket (TGT): The KDC verifies Alice's identity and issues her a Ticket Granting Ticket (TGT). 
The TGT contains:
Alice's identity
A session key shared between Alice and the KDC
A timestamp
The ticket is encrypted using the KDC's secret key.

**Step 3**: Ticket Request: Alice sends her TGT to the KDC, requesting a service ticket to access Bob's file server.

**Step 4**: Service Ticket: The KDC verifies Alice's TGT and issues her a service ticket for accessing Bob's file server.
The service ticket contains:
Alice's identity
Bob's identity (the server)
A session key shared between Alice and Bob
A timestamp
The service ticket is encrypted using Bob's secret key.

**Step 5**: Access Request: Alice sends the service ticket to Bob, along with a request to access the file server.

**Step 6**: Verification: Bob decrypts the service ticket using his secret key and verifies Alice's identity. If everything checks out, Bob grants Alice access to the file server.

**Session Establishment**:
Once the authentication is complete, Alice and Bob can communicate securely using the session key shared between them. This session key is used to encrypt and decrypt data exchanged between Alice and Bob during their communication session.

In summary, Kerberos enables secure authentication between clients and servers by utilizing tickets and session keys. It provides a robust mechanism for verifying identities and establishing secure communication channels over a non-secure network.
