
# **5 - Inter-VLAN Routing**

<br>

## **5.1 Introduction** 

Inter-VLAN routing ensures that individual VLANs can actually communicate with each other. The trunk links and access ports are already prepared, so the router can now take the role of the central gateway for all parts of the network. 

Subinterfaces are created on R1 for each VLAN, 802.1Q encapsulation is applied, and every VLAN receives its own gateway IP address. This connects the network at Layer 3 and allows all segments to communicate normally. This setup also prepares the environment for DHCP relay and other services that will be added in the upcoming chapters.

<br>

## **5.2 Topology Diagram**

![](images/Pasted%20image%2020251117161953.png)

<br>

## **5.3 Objectives**

1. The router is configured using subinterfaces, dot1Q encapsulation and gateway IPv4 addresses for all VLANs, enabling Layer 3 forwarding across the network.
    
2. Connectivity is verified through subinterface status checks and gateway ping tests to confirm that routing is active on R1.

<br>

## **5.4 Router Subinterface Configuration**

This main section defines the core router configuration required to enable inter-VLAN routing. The router creates logical subinterfaces for all VLANs, applies 802.1Q encapsulation and assigns gateway IPv4 addresses according to the addressing plan. These steps activate Layer 3 forwarding across the network and allow all internal segments to communicate beyond their local VLAN boundaries.

| **Subinterface (R1)** | **VLAN ID** | **Encapsulation (dot1Q)** | **Gateway IPv4** | **Assigned Segment** |
| --------------------- | ----------- | ------------------------- | ---------------- | -------------------- |
| Gi0/1.10              | 10          | dot1Q 10                  | 192.168.10.1     | Admin-Server         |
| Gi0/1.50              | 50          | dot1Q 50                  | 192.168.50.1     | Management           |
| Gi0/2.20              | 20          | dot1Q 20                  | 192.168.20.1     | Finance              |
| Gi0/2.30              | 30          | dot1Q 30                  | 192.168.30.1     | Guest                |
| Gi0/2.40              | 40          | dot1Q 40                  | 192.168.40.1     | Office               |
| Gi0/2.60              | 60          | dot1Q 60                  | 192.168.60.1     | Sales                |


### **Router R1**

- The router defines one subinterface per VLAN to provide a separate Layer 3 gateway.
    
- Each subinterface uses 802.1Q encapsulation with a tag matching the VLAN ID.
    
- Gateway IPv4 addresses are assigned according to the addressing plan.
    
- Layer 3 forwarding is enabled across all internal segments.
    

**Configuration in CLI:**

 ```
enable
configure terminal
interface gi0/1.10
encapsulation dot1Q 10
ip address 192.168.10.1 255.255.255.0
exit
interface gi0/1.50
encapsulation dot1Q 50
ip address 192.168.50.1 255.255.255.0
exit
interface gi0/2.20
encapsulation dot1Q 20
ip address 192.168.20.1 255.255.255.0
exit
interface gi0/2.30
encapsulation dot1Q 30
ip address 192.168.30.1 255.255.255.0
exit
interface gi0/2.40
encapsulation dot1Q 40
ip address 192.168.40.1 255.255.255.0
exit
interface gi0/2.60
encapsulation dot1Q 60
ip address 192.168.60.1 255.255.255.0
exit
end
write

 ```
![](images/Pasted%20image%2020251117152502.png)

### **Activation of Physical Interfaces**

This section enables the physical interfaces on R1 so that all subinterfaces can operate correctly. The router requires the parent interfaces to be in an up/up state; otherwise, the subinterfaces remain inactive and cannot route VLAN traffic.

#### **Router R1**

- The physical interfaces Gi0/1 and Gi0/2 are enabled.
    
- Both interfaces become operational and establish connectivity with the switches.
    
- All subinterfaces automatically activate once the parent interfaces are up.

 ```
 enable
configure terminal
interface gi0/1
no shutdown
exit
interface gi0/2
no shutdown
exit
end
write

 ```
![](images/Pasted%20image%2020251117154050.png)

### **Results**

All router subinterfaces are now active, correctly tagged and assigned to their gateway IPv4 addresses. R1 can forward traffic between all VLAN segments, and internal hosts are able to reach their default gateways as well as communicate across VLAN boundaries. The routing layer is fully operational and prepared for DHCP relay and further configurations in the next chapter.

<br>

## **5.4 Verification and Diagnostics**

This section verifies that the router subinterfaces operate correctly and that the routing layer is active. The router checks the status of all subinterfaces and tests reachability of the gateway IPs assigned to each VLAN. These tests confirm that inter-VLAN routing is ready for further configuration in the next chapter.

### **Router R1**

- The router shows all subinterfaces and their IPv4 addresses.
    

```
show ip interface brief 
```
![](images/Pasted%20image%2020251117154248.png)

>**Notes.:** The command _show vlans_ is also an important diagnostic option, but its output is too long to include in this documentation.

### **Gateway Ping Tests on Router R1**

This action verifies that the router can reach the gateway IPv4 addresses assigned to each VLAN. Successful replies confirm that all subinterfaces are active, correctly tagged and ready to route internal traffic once the hosts receive their IP configurations in the next chapter.

### **Router R1**

- The router tests the gateway address for each VLAN.
    
- Successful replies confirm that the subinterfaces operate correctly.
    
- *These tests do not require DHCP or host addressing.*

 ```
ping 192.168.10.1
ping 192.168.20.1
ping 192.168.30.1
ping 192.168.40.1
ping 192.168.50.1
ping 192.168.60.1
 ```
![](images/Pasted%20image%2020251117160449.png)

### **Results**

All gateway IP addresses respond correctly, confirming that R1 forwards traffic to every VLAN interface. The router is fully prepared for DHCP relay and client addressing in the next chapter.

<br>

## **5.5 Conclusion**

This chapter establishes full inter-VLAN routing across the network. The router creates and enables all required subinterfaces, applies 802.1Q encapsulation and assigns the gateway IPv4 addresses defined in the addressing plan. After activating the physical interfaces, every subinterface becomes operational and the router can forward traffic to all internal VLANs. Diagnostic tests confirm that gateway IPs are reachable and that the Layer 3 routing layer works correctly. The network is now prepared for DHCP relay and the distribution of client IPv4 addresses in the next chapter.


---

<br>

**Next chapter:** [DHCP Configuration](06-dhcp-configuration.md)

