# **7 - NAT/PAT and Static Routing**

<br>

## **7.1 Introduction**  

This chapter defines NAT, PAT and static routing on Router R1 and the ISP router. The goal is to translate all internal VLAN segments to the public address used on R1, so devices from the internal network can reach the Internet Host. R1 uses NAT overload on its outside interface, while the ISP router applies a static route that returns traffic back toward the internal subnets. The configuration assigns inside and outside roles, creates a simple NAT ACL for the internal ranges, and verifies connectivity using ping tests toward the Internet Host.

<br>

## **7.2 Topology Diagram**

![](images/Pasted%20image%2020251217030626.png)

<br>

## **7.3 Objectives**

1. Define static routes on R1 and the ISP router.
    
2. Assign NAT inside and outside roles on R1.
    
3. Create a NAT ACL for internal networks.
    
4. Apply PAT overload on the outside interface.
    
5. Verify connectivity and NAT operation using ping tests and translation commands.

<br>

## **7.4 Static Routing Configuration**


This part configures the static routes used by R1 and the ISP router. R1 sets a default route that forwards all external traffic toward the ISP router. The ISP router defines one route for each internal VLAN segment, so all internal networks return through R1. These routes create a clear path for outbound and return traffic and prepare the environment for NAT and PAT in the following steps.

### **Router R1**  

```
enable
configure terminal
ip route 0.0.0.0 0.0.0.0 203.0.113.2  
exit 
write
```
![](images/Pasted%20image%2020251118020753.png)

### **Router ISP**  

```
enable
configure terminal
ip route 192.168.10.0 255.255.255.0 203.0.113.1  
ip route 192.168.20.0 255.255.255.0 203.0.113.1  
ip route 192.168.30.0 255.255.255.0 203.0.113.1  
ip route 192.168.40.0 255.255.255.0 203.0.113.1  
ip route 192.168.50.0 255.255.255.0 203.0.113.1  
ip route 192.168.60.0 255.255.255.0 203.0.113.1  
end  
write
```
![](images/Pasted%20image%2020251118020945.png)


>**Notes.:** The return routes on the ISP router can also be summarized into one entry using prefix 192.168.0.0 with mask 255.255.0.0. The detailed /24 routes are used here for clarity and to show each VLAN as a separate network.


### **Results**  

Static routing is now fully defined. R1 forwards all non-local traffic to the ISP router, and the ISP router sends all VLAN subnets back toward R1 using individual routes. This establishes a clear routing path for all internal segments and forms the base for NAT and PAT configuration.

<br>

## **7.5 NAT Interface Assignment and ACL Configuration**


This part assigns NAT inside and outside roles to the correct interfaces on R1 and prepares the access list used for translation. All VLAN subinterfaces on the trunks to SW1 and SW2 are marked as inside interfaces, because they serve internal hosts. The interface toward the ISP router is marked as the outside interface, because it forwards translated traffic to the external network. After defining these roles, an ACL is created to identify the internal VLAN subnets that require NAT. These steps ensure that the PAT overload configuration can operate correctly and that NAT translations and statistics appear during verification.


### **NAT Inside/Outside Assignment on Router R1**  


```
enable  
configure terminal
interface gi0/0  
ip nat outside  
exit
interface gi0/1.10  
ip nat inside  
exit
interface gi0/1.50  
ip nat inside  
exit
interface gi0/2.20  
ip nat inside  
exit
interface gi0/2.30  
ip nat inside  
exit
interface gi0/2.40  
ip nat inside  
exit
interface gi0/2.60  
ip nat inside  
exit
end  
write
```
![](images/Pasted%20image%2020251118033309.png)

### **Router R1**  

```
enable
configure terminal
access-list 1 permit 192.168.10.0 0.0.0.255  
access-list 1 permit 192.168.20.0 0.0.0.255  
access-list 1 permit 192.168.30.0 0.0.0.255  
access-list 1 permit 192.168.40.0 0.0.0.255  
access-list 1 permit 192.168.50.0 0.0.0.255  
access-list 1 permit 192.168.60.0 0.0.0.255  
end  
write
```
![](images/Pasted%20image%2020251118022609.png)

>**Notes.:** The ACL can also be summarized using a single entry with prefix 192.168.0.0 and mask 0.0.255.255. The detailed VLAN entries are used here for clarity and to show each network individually.


### **Results**  

R1 now correctly identifies internal and external interfaces for NAT, and the NAT ACL selects all internal VLAN networks for translation. With these elements in place, the router is ready for the PAT overload configuration, and NAT translations and statistics can be generated during connectivity tests.


<br>

## **7.6 PAT Overload Configuration**


This part enables the actual address translation on R1. The router uses the NAT ACL created in the previous step and applies PAT overload on its outside interface. With overload active, all internal VLAN hosts share the same external address when sending traffic toward the Internet Host. This configuration completes the translation setup and prepares the router for connectivity verification.

### **Router R1**  

 ```
configure terminal
ip nat inside source list 1 interface gi0/0 overload  
exit 
write
 ```
![](images/Pasted%20image%2020251118024052.png)


>**Notes.:** The PAT rule uses ACL 1 to match internal networks, and the outside interface defines the address used for translation. This is the standard configuration for many-to-one address translation.

### **Results**  

PAT overload is now active on R1. All internal VLAN networks use the outside interface address when accessing external destinations, and the router translates multiple hosts through a single public address.

<br>

## **7.7 NAT/PAT Verification**


This part tests the end-to-end communication between internal VLAN hosts and the Internet Host. Ping tests confirm that internal devices forward traffic through R1 and the ISP router toward the external network. NAT verification commands confirm that R1 translates internal addresses through the outside interface using PAT overload. These checks confirm that routing and translation functions operate correctly.

### **Ping Tests from Internal Hosts**

* PC1 → Internet Host (198.51.100.2)  

```
ping 198.51.100.2
```
![](images/Pasted%20image%2020251118033808.png)

* PC2 → Internet Host (198.51.100.2)  

```
ping 198.51.100.2
```
![](images/Pasted%20image%2020251118033750.png)

### **Router R1**  

```
show ip nat translations  
show ip nat statistics
```
![](images/Pasted%20image%2020251118033719.png)

### **Results**  

Internal VLAN hosts successfully reach the Internet Host, and the replies return through R1 and the ISP router. The NAT table on R1 shows active translations, confirming that PAT overload operates correctly and that routing and NAT functions work as configured.

<br>

## **Final NAT/PAT Address Translation Overview**

This table summarizes how internal devices are represented on the external network through PAT. It shows the relationship between private internal addresses, their public translated form, and the external host accessed by the internal VLAN clients.


| **Term**           | **Example Value** | **Meaning**                                  | **Explanation**                                                                                                                      |
| ------------------ | ----------------- | -------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------ |
| **Inside Local**   | 192.168.10.10     | Internal private address                     | The actual IP address of a device inside the LAN. It exists only within the internal network and is never visible on the internet.   |
| **Inside Global**  | 203.0.113.1       | Public address representing internal devices | The public IP assigned to R1. Under this address all internal devices appear on the internet when PAT overload is active.            |
| **Outside Local**  | 198.51.100.2      | Internet Host as seen from inside            | The address of the external server as it appears from R1. In typical NAT configurations it is identical to the outside global value. |
| **Outside Global** | 198.51.100.2      | Real public address of the Internet Host     | The actual public IP address of the external host on the internet. This is its true, globally reachable address.                     |

<br>

## **7.8 Conclusion**

This chapter sets the complete path for routing and address translation between the internal VLAN networks and the Internet Host. R1 first defines static routes and uses the ISP router as the next hop for all external traffic. The ISP router returns all internal subnets back toward R1. After the routing path is ready, R1 assigns inside and outside interfaces, creates a simple NAT ACL, and applies PAT overload on its outside interface. Ping tests confirm that internal devices reach the Internet Host, and NAT translation commands show that R1 correctly translates all internal addresses through one public address. The routing and NAT functions operate as expected.


---

<br>

**Next chapter:** [Network Security](08-network-security.md)
