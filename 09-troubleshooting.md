

# **9 - Troubleshooting**

<br>

## **9.1 Introduction**

This chapter shows the main issues that appear during the project and explains how they are found, analysed, and fixed. Even when the network is designed correctly, small mistakes in VLANs, trunk links, routing, NAT, or ACL logic can stop communication between devices. Troubleshooting checks the symptoms, compares the expected behaviour with the actual state, and verifies every layer step by step. The goal is to identify where the traffic stops, confirm the root cause, and apply the correct fix.  

This chapter documents the real problems that appeared in the project and demonstrates the complete troubleshooting process: detection, analysis, correction and verification.

**Main issues in this project:**  
* Missing VLANs on SW2  
* Wrong trunk port between SW2 and SW3  
* Incorrect subnet mask in the ISP static route  
* ACL order blocking valid traffic from Guest VLAN

<br>

## **9.2 Missing VLANs on SW2**

### **Issue**  

Devices in VLAN 40 and VLAN 60 cannot reach their default gateway and missing DHCP. Other VLANs operate normally. 

The goal is to identify why these two segments fail while the rest of the network works as expected.


<br>

### **1) Ping Tests**

* ***PC4 - Ping Test**  

This test checks if PC4 can reach its default gateway in VLAN 40.

```
ping 192.168.40.1
```
![](images/Pasted%20image%2020251120212542.png)

Ping fails, which confirms that PC4 cannot reach the gateway.


* PC5 - Ping Test

This test verifies gateway reachability for PC5 in VLAN 40.

```
ping 192.168.40.1
```
![](images/Pasted%20image%2020251120212613.png)

Ping fails, indicating no forwarding for VLAN 40.


* ***PC6 - Ping Test**

This test checks gateway reachability for PC6 in VLAN 60.

```
ping 192.168.60.1
```
![](images/Pasted%20image%2020251120212701.png)

Ping fails, confirming that VLAN 60 is also affected.


<br>

### **3) Diagnostics**

We now verify the forwarding path and check whether the switches correctly support VLAN 40 and VLAN 60.

* **Trunk Verification on SW2**

This check confirms whether trunks on SW2 forward traffic correctly.

```
show interfaces trunk
```
![](images/Pasted%20image%2020251120212840.png)

Trunk links are active and pass the allowed VLANs. No trunk-related issue is found.

---

* **VLAN Database on SW3**

This step verifies that SW3 contains the expected VLAN entries.

```
show vlan brief
```
![](images/Pasted%20image%2020251120212907.png)

VLAN 40 and VLAN 60 are present on SW3, which confirms correct configuration on this switch.

---

* **VLAN Database on SW2**

This check confirms VLAN presence on SW2.

```
show vlan brief
```
![](images/Pasted%20image%2020251120212929.png)

**VLAN 40 and VLAN 60 are missing. This identifies the root cause of the failure!!!!.**


<br>

### **4) Fix**

Since SW2 is missing VLAN 40 and VLAN 60, these VLANs must be added to restore forwarding and allow DHCP traffic to reach R1.

```
configure terminal
vlan 40 
vlan 60
exit 
write memory
```
![](images/Pasted%20image%2020251120213112.png)

The missing VLANs are now created and stored in the running configuration.

<br>

### **5) Verification**

After applying the fix, we first verify the VLAN database with `show vlan brief`. Then we reconnect the VPCs in VLAN 40 to DHCP and test gateway reachability.

* **SW2 - VLAN Verification**

```
show vlan brief
```
![](images/Pasted%20image%2020251120213947.png)

VLAN 40 and VLAN 60 appear correctly in the VLAN database.

### * **PC4/PC5/PC6 - DHCP Verification**

* PC4*

```
ipdhcp
show ip
```
![](images/Pasted%20image%2020251120214247.png)

* **PC5**

```
ipdhcp
show ip
```
![](images/Pasted%20image%2020251120214311.png)

* **PC6ping**

```
ipconfig /renew
```
![](images/Pasted%20image%2020251120214424.png)

### **Ping test to gateway**

* **PC4 - Ping Verification

```
ping 192.168.40.1
```
![](images/Pasted%20image%2020251120214526.png)

Ping is successful.


* **PC5 - Ping Verification**

```
ping 192.168.40.1
```
![](images/Pasted%20image%2020251120214551.png)

Ping is successful.


* **PC6 - Ping Verification**

```
ping 192.168.60.1
```
![](images/Pasted%20image%2020251120214455.png)

Ping is successful.


**All devices now receive correct DHCP addresses.**

### **Results**

Forwarding for VLAN 40 and VLAN 60 is restored. DHCP allocation works correctly for all affected devices. Ping tests confirm full connectivity to the gateway. The issue was successfully diagnosed and resolved by adding the missing VLANs to SW2.


<br>

## 9.3 Wrong Trunk Port Between SW2 and SW3

## Issue

Devices connected behind SW3 cannot reach their default gateways or obtain DHCP addresses. Other VLANs in the network work correctly. The goal is to identify why traffic between SW2 and SW3 fails, even though VLANs are configured properly.

<br>

### 1) **Ping Tests**
   
This step checks communication from various devices across the network to determine if traffic passes between SW2 and SW3.

- _PC2 -> PC4_
    

```plaintext
ping 192.168.40.102
```
![](images/Pasted%20image%2020251120222310.png)

Ping fails, which confirms that VLAN 40 traffic does not pass between SW2 and SW3.

- _Windows Server -> PC5_
    

```plaintext
ping 192.168.40.101
```
![](images/Pasted%20image%2020251120215715.png)

Ping fails, confirming no communication with VLAN 40 devices behind SW3.

- _Admin PC -> PC6_
    

```plaintext
ping 192.168.60.100
```
![](images/Pasted%20image%2020251120215747.png)

Ping fails, indicating VLAN 60 connectivity is also affected.

<br>

### 2) **Diagnostics**

We verify trunking and VLAN propagation to locate the problem between SW2 and SW3.

- _SW2 - Trunk Status_
    

```plaintext
show interfaces trunk
```
![](images/Pasted%20image%2020251120215825.png)

Trunk is active only on Gi0/0, so VLANs cannot propagate if SW2 and SW3 are connected on a different interface.

- _SW2 - Switchport Mode_
    

```plaintext
show interfaces switchport
```
![](images/Pasted%20image%2020251120221353.png)

Interface Gi0/2 (connected to SW3) is in access mode, not trunk mode. Interface Gi0/3 is configured as a trunk but is unused.

- _SW2 - CDP Neighbor Check_
    

```plaintext
show cdp neighbors
```
![](images/Pasted%20image%2020251120221416.png)

The neighbor SW3 appears on Gi0/2, confirming that Gi0/2 is the physical uplink. The trunk configuration must therefore be moved from Gi0/3 to Gi0/2.

<br>

### **3) Fix**

The incorrect trunk configuration is removed from Gi0/3 and correctly applied to Gi0/2.

#### Switch SW2

**Configuration in CLI**

```plaintext
configure terminal
interface gi0/3
no switchport mode trunk
no switchport trunk allowed vlan 10,20,30,40,50,60
switchport mode access
exit
interface gi0/2
switchport mode trunk
switchport trunk allowed vlan 10,20,30,40,50,60
exit
write memory
```
![](images/Pasted%20image%2020251120221706.png)

The trunk configuration is now corrected and saved.

<br>

### 4) Verification

After the fix, connectivity is retested to confirm restored communication between SW2 and SW3.


- _PC2 -> PC4_
    

```plaintext
ping 192.168.40.102
```
![](images/Pasted%20image%2020251120221903.png)

Ping is successful.

- _Windows Server -> PC5_
    

```plaintext
ping 192.168.40.103
```
![](images/Pasted%20image%2020251120222055.png)

Ping is successful.

- _Admin PC -> PC6_
    

```plaintext
ping 192.168.60.100
```
![](images/Pasted%20image%2020251120222130.png)

Ping is successful.

- _SW2 - Trunk Verification_
    

```plaintext
show interfaces trunk
```
![](images/Pasted%20image%2020251120222207.png)

The output shows Gi0/2 as an active trunk carrying VLANs 20,30,40,60. Interface Gi0/3 is no longer used as a trunk.

<br>

### Results

The trunk connection between SW2 and SW3 now functions correctly. Devices behind SW3 regain access to their gateways and can communicate across VLANs. The diagnostics confirmed that the trunk was applied on the wrong interface, and relocating it to the correct port resolved the problem.

<br>
## **9.4 - Incorrect NAT Overload Interface**

### Issue

Devices in VLAN 60 cannot reach the Internet, while all other VLANs operate normally. Inter-VLAN communication inside the LAN is functional. The goal is to identify why external connectivity fails for VLAN 60 and resolve the NAT issue.

### 1) Ping Test

**PC6 - Internet Connectivity Test**  
This test checks whether VLAN 60 can reach the external IP 198.51.100.2.

```
ping 198.51.100.2
```
![](images/Pasted%20image%2020251120225605.png)

Ping fails, confirming that VLAN 60 cannot reach the Internet, while other VLANs succeed.

### **2) Diagnostics**

**R1 - NAT Translation Check**  
Check if inside local addresses are being translated to the public IP.

```
show ip nat translations
```
![](images/Pasted%20image%2020251120225624.png)

No NAT translations are shown, which indicates that NAT is not functioning correctly.

**R1 - NAT Configuration Review**  
Inspect the NAT configuration and verify the overload interface.

```
show run | include ip nat inside source
```
![](images/Pasted%20image%2020251120225654.png)

The output reveals a configuration error:

```
   ip nat inside source list 1 interface GigabitEthernet0/1 overload ← Incorrect interface
```

The overload is applied to interface Gi0/1, which connects to the internal LAN instead of the ISP uplink.

### **3) Fix**

To restore proper NAT operation, the overload must be applied to the correct external interface.

```
conf t
no ip nat inside source list 1 interface GigabitEthernet0/1 overload
ip nat inside source list 1 interface GigabitEthernet0/0 overload
end
write memory
```
![](images/Pasted%20image%2020251120225850.png)

### **4) Verification**

**Ping Test:**

```
ping 198.51.100.2
```
![](images/Pasted%20image%2020251120230006.png)

Ping is now successful, confirming restored Internet connectivity for VLAN 60.

**Check NAT Translations:**

```
show ip nat translations
```
![](images/Pasted%20image%2020251120230020.png)

NAT entries for VLAN 60 now appear correctly.

### **Results**

NAT failed because the overload was applied to the wrong interface (Gi0/1 instead of Gi0/0). After correcting the overload configuration, NAT translations function properly and all VLANs regain Internet access.

<br>

## 9.5 ACL Order Blocking Valid Traffic from Guest VLAN

### **Issue**

Devices in the Guest VLAN (VLAN 30) cannot reach the internal server even though the ACL policy should allow limited access. Internet access for VLAN 30 works normally. The goal is to identify and correct the ACL logic blocking valid internal traffic.

### 1) **Ping Tests**
    

This step verifies connectivity from the Guest VLAN to the internal Windows Server and the Internet.

- **PC3 (Guest VLAN 30) -> Windows Server (192.168.10.10)**
    

```plaintext
ping 192.168.10.10
```
![](images/Pasted%20image%2020251120231200.png)

Ping fails, which confirms that the ACL blocks access to the server.

- **PC3 (Guest VLAN 30) -> Internet Host (198.51.100.2)**
    

```plaintext
ping 198.51.100.2
```
![](images/Pasted%20image%2020251120231336.png)

Ping is successful, confirming that NAT and external access work correctly.

- **PC2 (VLAN 20) -> Windows Server (192.168.10.10)**
    

```plaintext
ping 192.168.10.10
```
![](images/Pasted%20image%2020251120231412.png)

Ping is successful, which means the server itself is reachable and functional.

### **2) Diagnostics**
    

---

We verify the ACL configuration and placement to find out why Guest VLAN cannot access the internal server.

- **Device: R1 - Interface Configuration Check**
    

```plaintext
show running-config interface gi0/2.30
```
![](images/Pasted%20image%2020251120231521.png)

The output shows the ACL applied inbound on subinterface Gi0/2.30 using the command:

```plaintext
ip access-group ACL-VLAN30 in
```

This confirms that the ACL for VLAN 30 is correctly attached to the interface.

- **Device: R1 - ACL Content Review**
    

```plaintext
show access-lists ACL-VLAN30
```
![](images/Pasted%20image%2020251120231603.png)

The ACL includes a deny rule that blocks all traffic to the Admin VLAN (192.168.10.0/24) before the specific permit rule allowing access to the Windows Server. Because of this order, the deny statement is matched first, preventing valid traffic to 192.168.10.10.

- **Device: R1 - ACL Hit Counters (optional)**
    

```plaintext
show access-lists ACL-VLAN30
```
![](images/Pasted%20image%2020251120231657.png)

Hit counters increase on the deny statement during ping tests, confirming that the ACL drops traffic before reaching the intended permit rule.

### 3) **Fix**
    


We correct the ACL order by placing the specific permit rule before the deny statements.

**Device: R1**

```plaintext
configure terminal
ip access-list extended ACL-VLAN30
no deny ip 192.168.30.0 0.0.0.255 192.168.10.0 0.0.0.255
permit ip 192.168.30.0 0.0.0.255 host 192.168.10.10
deny ip 192.168.30.0 0.0.0.255 192.168.10.0 0.0.0.255
permit ip any any
end
write memory
```
![](images/Pasted%20image%2020251120231754.png)

The ACL now correctly allows access to the Windows Server while denying other Admin VLAN traffic.

### **4) Verification**
    


Connectivity is retested from VLAN 30 to confirm that the ACL order fix restores access.

- **PC3 (Guest VLAN 30) -> Windows Server (192.168.10.10)**
    

```plaintext
ping 192.168.10.10
```
![](images/Pasted%20image%2020251120231834.png)

Ping is successful, confirming that the permit rule now takes precedence.

- **PC3 (Guest VLAN 30) -> Internet Host (198.51.100.2)**
    

```plaintext
ping 198.51.100.2
```
![](images/Pasted%20image%2020251120231859.png)

Ping remains successful, verifying that NAT and external access are unaffected.

- **Device: R1 - ACL Hit Counters**
    

```plaintext
show access-lists ACL-VLAN30
```
![](images/Pasted%20image%2020251120231934.png)

Hit counters increase on the permit statement for 192.168.10.10, confirming correct ACL logic.

### Results

Traffic from Guest VLAN 30 to the internal Windows Server (192.168.10.10) now works as intended, while all other internal destinations stay blocked. The issue was caused by a wrong ACL rule, and fixing it restored proper access without affecting Internet connectivity.


---

<br>

### **9.6 Conclusion**

This chapter tested and fixed common network issues. Problems with VLANs, trunk ports, NAT, and ACLs were found, analyzed, and corrected. Using commands like `show vlan`, `show interfaces trunk`, `show ip nat translations`, and `show access-lists`, the whole network was verified. After all fixes, every VLAN and NAT connection works, ACL rules are correct, and the network is fully stable and functional.

---

<br>

**Next part:** [Conclusion and Summary](10-conclusion-and-summary.md)




