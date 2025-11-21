# **8 - Network Security**

<br>

## **8.1 Introduction**

This important chapter presents the main security features used to protect the network. The goal is to secure all devices, limit access on switch ports, allow safe remote management and control communication between VLANs. First, the routers and switches are secured with passwords, console and VTY access rules, encrypted passwords and a message banner. These steps create basic protection against unauthorized access. Next, port security is applied to access ports. It limits the number of MAC addresses, prevents cable moving between ports and protects the switching layer from simple attacks. SSH is then configured to allow encrypted remote access. The chapter also shows an SSH connection from the Windows-Admin to Router R1 using PowerShell, including simple commands executed over SSH. 

After that, Access Control Lists define which VLANs can communicate. Guest traffic is restricted, admin traffic is allowed, and other segments follow the planned rules for safe communication. The chapter ends with diagnostics and verification to confirm that all security functions work correctly.

<br>

## **8.2 Topology Diagram**

![](images/Pasted%20image%2020251120015512.png)

<br>

## **8.3 Obectives**

1. Secure all routers and switches with enable secret, console password, VTY password and password encryption.
    
2. Configure the message-of-the-day banner for basic access warning.
    
3. Apply port security on access ports, set 1 MAC per port and enable sticky MAC with the chosen violation mode.
    
4. Configure SSH on Cisco devices by setting the domain name, generating RSA keys, creating the admin user and enabling SSH on VTY lines.
    
5. Testing SSH access from Windows-Admin to Router R1 using PowerShell.
    
6. Create and apply ACL rules that control communication between VLANs and restrict Guest access.
    
7. Test communication after ACL deployment to verify allowed and blocked paths between VLAN segments.

<br>

## **8.4 Basic Access Security**


This section secures local access to all network devices. A unified administrator account is created, console access is protected with a password, and privileged mode is secured with an enable secret. Password encryption is enabled to protect stored credentials.  
These steps provide the initial security layer before port security, SSH access and ACL rules are introduced in later parts of this chapter.


### **Security Credentials Overview**

| Access Type      | Username  | Password                       |
| ---------------- | --------- | ------------------------------ |
| Console Login    | **admin** | **12345**                      |
| Console Password | N/A       | **cisco**                      |
| SSH Login        | **admin** | **12345** _(configured later)_ |
| Enable Secret    | N/A       | **cisco12345**                 |

<br>

Console password works as the entry protection for line console.  
After entering the console password, the device uses local authentication with the admin account.

>**Notes.:** Simple passwords are used for demonstration in this project.  
Real networks always require long, complex passwords with a mix of characters and symbols.


<br>

### **Security settings to configure:**  

*(These steps apply to R1, SW1, SW2 and SW3.)*

1. Set the enable secret for privileged access.
    
2. Configure console password and enable local login.
    
3. Add a timeout for console inactivity.
    
4. Enable password encryption.
    
5. Create the admin user with privilege 15.


<br>

### **Configuration consistency**

**The same configuration logic applies to all switches and router where relevant. Identical configurations are not repeated in this documentation to maintain clarity and avoid duplication.**

### **Router R1**

```
enable
configure terminal
enable secret cisco12345
line console 0
password cisco
login local
exec-timeout 5 0
exit
service password-encryption
username admin privilege 15 secret 12345
end
write
```
![](images/Pasted%20image%2020251118195539.png)

### **Verification – Basic Access Security**

```
R1 login: Username: admin Password: cisco12345
```

The router accepts the username and password and allows access without errors. This shows that the user login and basic access security work correctly.

### **Results**

All switches and Router R1 now use the same administrator account for console authentication. Privileged mode is protected with an enable secret, and stored passwords are encrypted. Console sessions automatically close after inactivity. This creates a consistent and secure baseline before adding further security features in the next steps.

<br>

## **8.5 Message Banner Configuration**


This step adds a security message-of-the-day banner to every router and switch. The banner informs users that the device is restricted and that unauthorized access is not allowed.  
It is a simple but important part of basic security, because it defines the legal warning before any login attempt.

### **Steps to Configure:**

1. Define a message-of-the-day banner on all devices.
    
2. Use a clear and short security warning.
    
3. Apply the same banner on R1, SW1, SW2 and SW3 for consistency.
    

<br>

### **Configuration consistency**

**The same configuration logic applies to all switches and router where relevant. Identical configurations are not repeated in this documentation to maintain clarity and avoid duplication.**

### **Router R1**

```
banner motd # Access is restricted to authorized users. #
```
![](images/Pasted%20image%2020251118195928.png)

### **Results:**

All network devices now display a security warning before login.  
This creates a consistent legal notice across the topology and completes the basic access protection layer.

<br>

## **8.6 Port Security**


This step applies port security on all access ports to limit each port to one device, block unauthorized cable swaps, and protect the switching layer from simple Layer 2 attacks. Sticky MAC learning is used so the switch automatically saves the connected device’s MAC address.


### **Steps to Configure**

*(These steps apply to SW1, SW2 and SW3.)*

1. Set a maximum of one MAC address per access port.
    
2. Enable sticky MAC learning.
    
3. Use the restrict violation mode.
    
4. Apply the configuration on all host-facing ports.
    


### **Switch SW1**

**Ports to secure:**  
* *gi0/0 (Windows-Server)  
* gi0/3 (Windows-Admin)  
* gi0/2 (PC1)

 ```
enable
configure terminal
interface gi0/0
switchport mode access
switchport port-security
switchport port-security maximum 1
switchport port-security mac-address sticky
switchport port-security violation restrict
exit
interface gi0/3
switchport mode access
switchport port-security
switchport port-security maximum 1
switchport port-security mac-address sticky
switchport port-security violation restrict
exit
interface gi0/2
switchport mode access
switchport port-security
switchport port-security maximum 1
switchport port-security mac-address sticky
switchport port-security violation restrict
exit
end
write

 ```
![](images/Pasted%20image%2020251118200154.png)

### **Switch SW2**

**Ports to secure:**  
* gi0/1 (PC2)  
* gi0/3 (PC3)

 ```
enable
configure terminal
interface gi0/1
switchport mode access
switchport port-security
switchport port-security maximum 1
switchport port-security mac-address sticky
switchport port-security violation restrict
exit
interface gi0/3
switchport mode access
switchport port-security
switchport port-security maximum 1
switchport port-security mac-address sticky
switchport port-security violation restrict
exit
end
write

 ```
![](images/Pasted%20image%2020251118200434.png)

## **Switch SW3**

**Ports to secure:**  
* gi0/1 (PC5)  
* gi0/2 (PC4)  
* gi0/3 (Windows-PC6)

 ```
enable
configure terminal
interface gi0/1
switchport mode access
switchport port-security
switchport port-security maximum 1
switchport port-security mac-address sticky
switchport port-security violation restrict
exit
interface gi0/2
switchport mode access
switchport port-security
switchport port-security maximum 1
switchport port-security mac-address sticky
switchport port-security violation restrict
exit
interface gi0/3
switchport mode access
switchport port-security
switchport port-security maximum 1
switchport port-security mac-address sticky
switchport port-security violation restrict
exit
end
write

 ```
![](images/Pasted%20image%2020251118200501.png)

## **Results**

All host access ports now allow only one device and use sticky learning to store MAC addresses automatically. Unauthorized devices on these ports trigger restriction mode, and the switches continue forwarding valid traffic. The switching layer is protected and prepared for SSH and ACL configuration in the next steps.

<br>

## **8.7 SSH Configuration**


This step enables secure remote access to Router R1 using SSH.  
SSH provides encrypted management access and prevents unauthorized users from controlling the network.  
Windows-Admin acts as the management workstation and connects to R1 using its built-in SSH client.

Only Router R1 requires an active SSH server.  
Windows-Admin uses the default OpenSSH client included in Windows 10 Enterprise.


### **Steps to Configure**

1. Set a domain name for crypto generation.
    
2. Generate RSA keys.
    
3. Enable SSH version 2.
    
4. Apply local login authentication to VTY lines.
    
5. Set an inactivity timeout on VTY access.
    
6. Test SSH access from Windows-Admin to Router R1.


### **Configuration ssh on Router R1**

 ```
enable
configure terminal
ip domain-name local
crypto key generate rsa 
1024
ip ssh version 2
line vty 0 4
login local
transport input ssh
exec-timeout 10 0
exit
end
write
 ```
![](images/Pasted%20image%2020251118201047.png)

### **Results** 

R1 now accepts encrypted SSH sessions using the local admin account.  
VTY access is protected with a timeout, and all remote management is secured.

<br>

## **Windows-Admin – SSH Access to R1**

Windows-Admin uses PowerShell to establish a secure SSH session with R1. No server installation is needed — Windows includes an SSH client by default.


**SSH Connection Test:**

```
ssh admin@203.0.113.1
```
![](images/Pasted%20image%2020251118225033.png)

### **Diagnostics on R1**

After logging into R1, run two basic tests to confirm device status and connectivity.

1. **Interface Status Overview**
2. **2. Connectivity Test to End Device (PC3 – VLAN 30)**

 ```
show ip interface brief
ping 192.168.30.101
 ```
![](images/Pasted%20image%2020251118225121.png)

Verifies operational state of all router interfaces and subinterfaces. Confirms inter-VLAN routing and reachability from the router to the end host.

### **Final results**

Secure SSH access from Windows-Admin to R1 works correctly.  
Interface status and connectivity tests confirm that R1 routes traffic between VLANs as intended. This completes the remote-management part of the security configuration and prepares the network for ACL deployment in the next step.

<br>

## **8.8 Access Control Lists (ACL)**


This chapter applies extended Access Control Lists on Router R1 to define the security rules between VLANs. The goal is to isolate user VLANs while keeping controlled access to shared services. VLAN 10 (Admin + Server) has full access to the network, and VLAN 50 (PC1) acts as a light management VLAN that can run diagnostics. All user VLANs allow ICMP echo-reply toward VLAN 10 and VLAN 50, so the administrator can check connectivity. 

Each user VLAN denies traffic to VLAN 10 and VLAN 50, except for echo-reply and controlled access to the Windows Server (192.168.10.10). Guest VLAN 30 stays isolated but also allows server access and Internet access through NAT/PAT. VLAN 10 does not apply an ACL and has unrestricted access.

<br>

### **ACL POLICY OVERVIEW**

| VLAN ID / Name / PC                  | Access to VLAN 10 (Admin) | Access to Server 10.10<br> | Inter-VLAN Access     | Echo-reply from VLAN 10 | Echo-reply from VLAN 50 | Internet Access |
| ------------------------------------ | ------------------------- | -------------------------- | --------------------- | ----------------------- | ----------------------- | --------------- |
| VLAN 20  PC20 -Finance               | Deny                      | Allow                      | Deny <br>30,40,50,60  | Deny                    | Deny                    | Allow           |
| VLAN 30  <br>PC30 - Guest            | Deny                      | Allow                      | Deny all              | Deny                    | Deny                    | Allow           |
| VLAN 40  PC40/50 -Offices            | Deny                      | Allow                      | Deny 20,30,50,60      | Deny                    | Deny                    | Allow           |
| VLAN 50  PC1 -Management             | Deny to VLAN 10           | Allow                      | Allow <br>20,30,40,60 | Deny                    | Aloow                   | Allow           |
| VLAN 60  PC60-Sales                  | Deny                      | Allow                      | Deny <br>20,30,40,50  | Deny                    | Deny                    | Allow           |
| VLAN 10 WIndows PCs - Admin + Server | Allow                     | Allow                      | Allow                 | Allow                   | Allow                   | Allow           |

<br>

### **ROUTER R1 CONFIGURATION**


### **VLAN 20 - ACL-VLAN20** (Finance) 

 ```
configure terminal
ip access-list extended ACL-VLAN20
permit udp any eq 68 any eq 67
permit udp any eq 67 any eq 68  
permit icmp 192.168.20.0 0.0.0.255 192.168.10.0 0.0.0.255 echo-reply  
permit icmp 192.168.20.0 0.0.0.255 192.168.50.0 0.0.0.255 echo-reply  
permit ip 192.168.20.0 0.0.0.255 host 192.168.10.10  
deny ip 192.168.20.0 0.0.0.255 192.168.10.0 0.0.0.255  
deny ip 192.168.20.0 0.0.0.255 192.168.50.0 0.0.0.255  
deny ip 192.168.20.0 0.0.0.255 192.168.30.0 0.0.0.255  
deny ip 192.168.20.0 0.0.0.255 192.168.40.0 0.0.0.255  
deny ip 192.168.20.0 0.0.0.255 192.168.60.0 0.0.0.255  
permit ip 192.168.20.0 0.0.0.255 any  
exit
interface gi0/2.20  
ip access-group ACL-VLAN20 in  
exit
end
write memory
 ```
![](images/Pasted%20image%2020251120005504.png)

### **VLAN 30 - ACL-VLAN30** (Guest)

 ```
configure terminal
ip access-list extended ACL-VLAN30
permit udp any eq 68 any eq 67
permit udp any eq 67 any eq 68  
permit icmp 192.168.30.0 0.0.0.255 192.168.10.0 0.0.0.255 echo-reply  
permit icmp 192.168.30.0 0.0.0.255 192.168.50.0 0.0.0.255 echo-reply  
permit ip 192.168.30.0 0.0.0.255 host 192.168.10.10  
deny ip 192.168.30.0 0.0.0.255 192.168.10.0 0.0.0.255  
deny ip 192.168.30.0 0.0.0.255 192.168.50.0 0.0.0.255  
deny ip 192.168.30.0 0.0.0.255 192.168.20.0 0.0.0.255  
deny ip 192.168.30.0 0.0.0.255 192.168.40.0 0.0.0.255  
deny ip 192.168.30.0 0.0.0.255 192.168.60.0 0.0.0.255  
permit ip 192.168.30.0 0.0.0.255 any  
exit
interface gi0/2.30  
ip access-group ACL-VLAN30 in  
exit
end
write memory
 ```
![](images/Pasted%20image%2020251120005603.png)


### **VLAN 40 - ACL-VLAN40** (Offices)

 ```
configure terminal
ip access-list extended ACL-VLAN40
permit udp any eq 68 any eq 67
permit udp any eq 67 any eq 68  
permit icmp 192.168.40.0 0.0.0.255 192.168.10.0 0.0.0.255 echo-reply  
permit icmp 192.168.40.0 0.0.0.255 192.168.50.0 0.0.0.255 echo-reply  
permit ip 192.168.40.0 0.0.0.255 host 192.168.10.10  
deny ip 192.168.40.0 0.0.0.255 192.168.10.0 0.0.0.255  
deny ip 192.168.40.0 0.0.0.255 192.168.50.0 0.0.0.255  
deny ip 192.168.40.0 0.0.0.255 192.168.20.0 0.0.0.255  
deny ip 192.168.40.0 0.0.0.255 192.168.30.0 0.0.0.255  
deny ip 192.168.40.0 0.0.0.255 192.168.60.0 0.0.0.255  
permit ip 192.168.40.0 0.0.0.255 any  
exit
interface gi0/2.40  
ip access-group ACL-VLAN40 in  
exit
end
write memory
 ```
![](images/Pasted%20image%2020251120005631.png)

### **VLAN 50 – ACL-VLAN50** (Management)

 ```
configure terminal
ip access-list extended ACL-VLAN50
permit udp any eq 68 any eq 67
permit udp any eq 67 any eq 68  
permit icmp 192.168.50.0 0.0.0.255 192.168.10.0 0.0.0.255 echo-reply  
permit ip 192.168.50.0 0.0.0.255 host 192.168.10.10  
deny ip 192.168.50.0 0.0.0.255 192.168.10.0 0.0.0.255  
permit ip 192.168.50.0 0.0.0.255 any  
exit
interface gi0/1.50  
ip access-group ACL-VLAN50 in  
exit
end
write memory

 ```
![](images/Pasted%20image%2020251120005702.png)

### **VLAN 60 – ACL-VLAN60** (Sales)

 ```
configure terminal
ip access-list extended ACL-VLAN60
permit udp any eq 68 any eq 67
permit udp any eq 67 any eq 68  
permit icmp 192.168.60.0 0.0.0.255 192.168.10.0 0.0.0.255 echo-reply  
permit icmp 192.168.60.0 0.0.0.255 192.168.50.0 0.0.0.255 echo-reply  
permit ip 192.168.60.0 0.0.0.255 host 192.168.10.10  
deny ip 192.168.60.0 0.0.0.255 192.168.10.0 0.0.0.255  
deny ip 192.168.60.0 0.0.0.255 192.168.50.0 0.0.0.255  
deny ip 192.168.60.0 0.0.0.255 192.168.20.0 0.0.0.255  
deny ip 192.168.60.0 0.0.0.255 192.168.30.0 0.0.0.255  
deny ip 192.168.60.0 0.0.0.255 192.168.40.0 0.0.0.255  
permit ip 192.168.60.0 0.0.0.255 any  
exit
interface gi0/2.60  
ip access-group ACL-VLAN60 in  
exit
end
write memory
 ```
![](images/Pasted%20image%2020251120005727.png)
<br>

### **Results**

The ACL rules isolate all user VLANs from VLAN 10 and VLAN 50 while still allowing controlled access to the Windows Server. Each user VLAN answers diagnostic pings from both administrative VLANs. Guest VLAN remains isolated but still supports server access and Internet connectivity. VLAN 50 can test the entire network but cannot reach VLAN 10. **VLAN 10 has full access to all VLANs and services across the network.** NAT/PAT stays fully functional for all VLANs.

<br>

## **8.9 ACL Communication Testing**


This step verifies that the ACLs on Router R1 work correctly. Each device tests communication to other VLANs to confirm allowed traffic (server access, echo-reply, Internet) and blocked traffic (admin protection, user VLAN isolation). Router diagnostics using show ip access-lists confirm that all ACLs are applied correctly. All tests follow the ACL policy defined in Chapter 08.

### **Testing Steps**

1. Test diagnostics on R1 with command show access-lists
    
2. Test echo-reply from all VLANs toward VLAN 10 and VLAN 50.
    
3. Test server access (192.168.10.10) from all user VLANs.
    
4. Test user VLAN isolation (20/30/40/60).
    
5. Test blocked access to VLAN 10 and VLAN 50 from user VLANs.
    
6. Test Internet reachability through NAT/PAT.


### **Router R1 Diagnostics**

To verify correct ACL operation on R1, the following commands are used:

```
show access-lists
```
![](images/Pasted%20image%2020251120005848.png)

All ACLs operate correctly, the router applies them as intended, and the network communication fully follows the designed security policy.

>**Notes.:** The command `show ip access-lists` can also be used to review the ACL entries with additional interface-related context, but it is not included in this documentation to keep the diagnostics section focused and concise.”

<br>

### **Windows-Server – VLAN 10 (192.168.10.10)**
 
Allowed: all hosts reply  
**Reason:** VLAN 10 is unrestricted and all VLANs permit echo-reply back to VLAN 10.

 ```
ping 192.168.20.100     (reply)
ping 192.168.30.101     (reply)
ping 192.168.40.102     (reply)
ping 192.168.40.103     (reply)
ping 192.168.50.100     (reply)
ping 192.168.60.100     (reply)

 ```
![](images/Pasted%20image%2020251120010351.png)
![](images/Pasted%20image%2020251120010444.png)

### **Windows-Admin – VLAN 10 (192.168.10.11)**

**Allowed:** all hosts reply (full access)  
**Reason:** VLAN 10 has unrestricted access, no ACL applied.

 ```
ping 192.168.20.100   (reply)
ping 192.168.30.101   (reply)
ping 192.168.40.102   (reply)
ping 192.168.40.103   (reply)
ping 192.168.50.100   (reply)
ping 192.168.60.100   (reply)

 ```
![](images/Pasted%20image%2020251120011304.png)
![](images/Pasted%20image%2020251120011400.png)

### **PC1 – VLAN 50 Management (192.168.50.100)**

**Allowed:** VLAN 20, 30, 40, 60  
**Blocked:** VLAN 10  
**Reason:** management VLAN, limited from admin subnet.

 ```
ping 192.168.20.100   (reply)
ping 192.168.30.101   (reply)
ping 192.168.40.102   (reply)
ping 192.168.40.103   (reply)   
ping 192.168.60.100   (reply)
ping 192.168.10.11    (blocked)
 ```
![](images/Pasted%20image%2020251120011556.png)

### **PC2 – VLAN 20 Finance (192.168.20.100)**

**Allowed:** server  
**Blocked:** all user VLANs

 ```
ping 192.168.10.11    (blocked)
ping 192.168.50.100   ((blocked)
ping 192.168.10.10    (allowed – server)
ping 192.168.30.101   (blocked)
ping 192.168.40.102   (blocked)
ping 192.168.40.103   (blocked)
ping 192.168.60.100   (blocked)

 ```
![](images/Pasted%20image%2020251120011815.png)

### **PC3 – VLAN 30 Guest – (192.168.30.101**)

**Allowed:** server  
**Blocked:** all user VLANs

 ```
ping 192.168.10.11    (blocked)
ping 192.168.50.100   (blocked)
ping 192.168.10.10    (allowed – server)
ping 192.168.20.100   (blocked)
ping 192.168.40.102   (blocked)
ping 192.168.40.103   (blocked)
ping 192.168.60.100   (blocked)

 ```
![](images/Pasted%20image%2020251120011946.png)

### **PC4 – VLAN 40 Office (192.168.40.102)**

**Allowed:** server  
**Blocked:** VLAN 20/30/50/60/windows-Admin

 ```
ping 192.168.10.11    (blocked)
ping 192.168.50.100   (blocked)
ping 192.168.10.10    (allowed – server)
ping 192.168.20.100   (blocked)
ping 192.168.30.101   (blocked)
ping 192.168.60.100   (blocked)

 ```
![](images/Pasted%20image%2020251120012048.png)

### **Windows-PC6 – VLAN 60 Sales (192.168.60.100)**

**Allowed:** server  
**Blocked:** all user VLANs

 ```
ping 192.168.10.11    (blocked)
ping 192.168.50.100   (blocked)
ping 192.168.10.10    (allowed – server)
ping 192.168.20.100   (blocked)
ping 192.168.30.101   (blocked)
ping 192.168.40.102   (blocked)
ping 192.168.40.103   (blocked)
 ```
![](images/Pasted%20image%2020251120013319.png)
![](images/Pasted%20image%2020251120013359.png)

### **Short Internet Connectivity Test (NAT/PAT)****

**Source:** PC2 VLAN 20  
**Allowed:** all VLANs  
**Reason:** NAT/PAT rules permit outbound traffic from internal subnets.

 ```
ping 198.51.100.2    (allowed)
 ```
![](images/Pasted%20image%2020251120013450.png)

>**Note:** The packet is translated by NAT/PAT on R1, confirming correct Internet reachability.

<br>

### **Results**

The testing confirms that all ACL rules operate exactly as intended. User VLANs stay isolated, the admin VLAN receives diagnostic replies from the entire network, and VLAN 50 can test all user VLANs without accessing the admin subnet. All user VLANs can reach the server and the Internet through NAT/PAT translation. Router diagnostics with show ip access-lists confirm that each ACL is applied correctly on the subinterfaces. The communication behavior now fully matches the designed security policy.

<br>

## **8.10 Conclusion**

The security phase builds the final protection layer of the network in a clear sequence. It begins with basic security measures such as device passwords, access control and the MOTD banner. Port security is then applied to restrict unauthorized physical connections. SSH is enabled afterwards to secure remote management. The last step deploys ACL rules that control communication between all VLAN segments. 

These controls complement the earlier design of topology, addressing, routing and services. With ACLs, DHCP, inter-VLAN routing and NAT/PAT all working correctly, the network now operates exactly according to the intended policy and design. The next two chapters, Troubleshooting and the Final Summary, complete the overall structure of the project.



---

<br>

**Next chapter:** [Troubleshooting](09-troubleshooting.md)









