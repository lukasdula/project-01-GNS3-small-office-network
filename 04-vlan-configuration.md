# **4 - VLAN Configuration**

<br>

## **4.1 Introduction**

VLANs (Virtual Local Area Networks) divide the network into separate logical segments, improving management, security, and traffic isolation. Each VLAN functions as an independent broadcast domain, allowing precise control over Layer 2 communication.

This chapter defines the creation of all VLANs and the assignment of switch ports according to the topology. Access ports are configured for user PCs, the server, and the admin workstation, while trunk links are prepared between switches and the router R1 to carry all VLANs across the switching layer.

Routing and subinterfaces are not configured in this chapter. Inter-VLAN routing will be implemented in the next section.

<br>

## **4.2 Topology Diagram**

![](images/Pasted%20image%2020251217025615.png)

<br>

## **4.3 VLAN Overview**

| **VLAN ID** | **VLAN Name** | **Description / Purpose**                  | **Assigned Devices**          | **Switch Association** |
| :---------- | :------------ | :----------------------------------------- | :---------------------------- | :--------------------- |
| **10**      | Admin-Server  | Windows Server and Admin PC, core services | Windows-Server, Windows-Admin | **SW1**                |
| **20**      | Finance       | Finance workstation                        | PC2                           | **SW2**                |
| **30**      | Guest         | Guest network with restricted access       | PC3                           | **SW2**                |
| **40**      | Office        | Office user workstations                   | PC4, PC5                      | **SW3/SW2**            |
| **50**      | Management    | Dedicated management workstation           | PC1                           | **SW1**                |
| **60**      | Sales         | Sales workstation segment                  | Windows-PC6                   | **SW3/SW2**            |

<br>

## **4.3 Objectives**

1. Create VLANs on all switches (SW1, SW2, SW3)
    
2. Assign access ports to host devices
    
3. Configure trunk ports between switches and R1
    
4. Verify VLAN and trunk configuration

<br>

## **4.4 VLAN Creation on Switches**

This step creates all required VLANs and their names on the switching layer. Each switch receives the full VLAN list to keep Layer 2 segmentation consistent across the topology. The VLANs are created without assigning ports, and port allocation happens in the next step. All VLANs are present on every switch to ensure correct trunk operation and forwarding between switches.

#### **Configuration in CLI:**

### **Switch SW1**

- Creation of VLANs required for hosts connected to SW1.
    

 ```
enable
configure terminal
vlan 10
name Admin-Server
exit
vlan 50
name Management
exit
end
write

 ```
![](images/Pasted%20image%2020251117034316.png)

### **Switch SW2**

- Creation of VLANs required for hosts connected to SW2.
    

 ```
enable
configure terminal
vlan 20
name Finance
exit
vlan 30
name Guest
exit
end
write

 ```
![](images/Pasted%20image%2020251117034635.png)

### **Additional VLANs Required for Transit Through SW2**

SW2 must create VLAN 40 and VLAN 60 even though no local hosts use them. These VLANs pass through SW2 from SW3 toward the router, so the switch must know them in its VLAN database.  
This ensures that trunk links forward traffic for VLAN 40 and VLAN 60 correctly.

 ```
enable 
configure terminal 
vlan 40 
name Office 
exit 
vlan 60  
name Sales 
exit 
end 
write
 ```
![](images/Pasted%20image%2020251117052740.png)

### **Switch SW3**

- Creation of VLANs required for hosts connected to SW3.
    

 ```
enable
configure terminal
vlan 40
name Office
exit
vlan 60
name Sales
exit
end
write

 ```
![](images/Pasted%20image%2020251117034742.png)


### **Conclusion**

All required VLANs have been created according to the logical network design. SW1 contains VLANs 10 and 50, SW2 contains VLANs 20 and 30, and SW3 contains VLANs 40 and 60. SW2 additionally includes VLANs 40 and 60, which are required for transit across trunk links even though no local hosts belong to these segments. The VLAN database on each switch is now fully prepared for the upcoming access port configuration.

<br>

## **4.5 Assign Access Ports to VLANs**

This step assigns the switch access ports to their correct VLANs according to the physical topology. Each host receives an access port mapped to the VLAN that matches its user segment. This ensures correct Layer 2 separation before trunk configuration.

### **Switch SW1**

- Access ports configured for the hosts connected to SW1.

```
enable
configure terminal
interface gi0/0
switchport mode access
switchport access vlan 10
exit
interface gi0/3
switchport mode access
switchport access vlan 10
exit
interface gi0/2
switchport mode access
switchport access vlan 50
exit
end
write
```
![](images/Pasted%20image%2020251117040020.png)

### **Switch SW2**

* Access ports configured for the hosts connected to SW2.

```
enable
configure terminal
interface gi0/1
switchport mode access
switchport access vlan 20
exit
interface gi0/3
switchport mode access
switchport access vlan 30
exit
end
write
```
![](images/Pasted%20image%2020251117040140.png)

### **Switch SW3**

- Access ports configured for the hosts connected to SW3.

```
enable
configure terminal
interface gi0/1
switchport mode access
switchport access vlan 40
exit
interface gi0/2
switchport mode access
switchport access vlan 40
exit
interface gi0/3
switchport mode access
switchport access vlan 60
exit
end
write
```
![](images/Pasted%20image%2020251117040259.png)

### **Results**

All access ports are now assigned to their correct VLANs based on the network design.  
The access layer follows the planned segmentation and provides proper VLAN separation for all connected hosts.  
These assignments prepare the switching layer for the upcoming trunk configuration.

<br>

## **4.6 Configure Trunk Ports**

This step configures the trunk links that connect the switches and the router. Trunk ports forward tagged VLAN traffic between devices and form the main path for communication across the Layer 2 topology. Each trunk allows only the VLANs that are required along that link, based on the VLAN planning and physical topology.

### **Switch SW1**

- Trunk port configured for the uplink toward router R1.
    
- These trunk settings follow the planned design for this switch.

```
enable
configure terminal
interface gi0/1
switchport trunk encapsulation dot1q
switchport mode trunk
switchport trunk allowed vlan 10,50
exit
end
write
```
![](images/Pasted%20image%2020251117205453.png)

### **Switch SW2**

- Trunk port toward R1 carries all VLANs used behind SW2 and SW3.
    
- Trunk port toward SW3 carries only VLANs present between SW2 and SW3.


```
enable
configure terminal
interface gi0/2
switchport trunk encapsulation dot1q
switchport mode trunk
switchport trunk allowed vlan 20,30,40,60
exit
interface gi0/0
switchport trunk encapsulation dot1q
switchport mode trunk
switchport trunk allowed vlan 40,60
exit
write
```
![](images/Pasted%20image%2020251117043826.png)

### **Switch SW3**

- Trunk port configured for the uplink toward SW2.
    
- The trunk allows only the VLANs used on SW3.

```
enable
configure terminal
interface gi0/0
switchport trunk encapsulation dot1q
switchport mode trunk
switchport trunk allowed vlan 40,60
exit
end
write
```
![](images/Pasted%20image%2020251117043944.png)

### **Results**

The trunk ports are now configured according to the network design and forward only the VLANs required for communication across the topology.  
The trunks support tagged traffic between the switches and the router and maintain the planned Layer 2 segmentation.  
These configurations prepare the switching layer for inter-VLAN routing in the next chapter.



>**Notes.:** The switching platform used in this project is based on the **vIOS-L2 image compiled in 2017**, which operates as a hybrid Layer
>2/Layer 3 software model. Unlike newer IOSv-L2 releases, this version requires explicit configuration of **802.1Q encapsulation** and manual definition of **allowed VLANs** on trunk ports. This behavior is normal for this image and ensures that trunk links handle VLAN tagging correctly and forward only the VLANs defined by the network design.

<br>

## **4.7 Verify VLAN and Trunk Configuration**


This step verifies that all VLANs and trunk links on the switching layer are configured correctly. Each switch is checked using three key show commands that confirm the VLAN database, access port assignments, and trunk status. These outputs ensure that the Layer 2 topology is ready for inter-VLAN routing in the next chapter.

### **Switch SW1**


- `show vlan brief`  
    Displays the full VLAN table on the switch, including VLAN IDs, VLAN names, and access port assignments.
    
- `show interfaces trunk`  
    Shows all active trunk ports, their operational state, and the VLANs allowed on each trunk link.
    
- `show interfaces status`  
    Provides an overview of every physical port, its link state, administrative mode, and whether it operates as access or trunk.
    

```
show vlan brief 
show interfaces trunk 
show interfaces status
```
![](images/Pasted%20image%2020251117045008.png)

### **Switch SW2**

```
show vlan brief 
show interfaces trunk 
show interfaces status
```
![](images/Pasted%20image%2020251117053237.png)

### **Switch SW3**

```
show vlan brief 
show interfaces trunk 
show interfaces status
```
![](images/Pasted%20image%2020251117045156.png)

### **Results**

All switches now report the correct VLANs, access port mappings, and trunk states. The VLAN database matches the physical topology, trunk links forward the expected traffic, and all access ports are assigned to their appropriate segments. The Layer 2 configuration is consistent across the switching plane and ready for the implementation of inter-VLAN routing.

<br>

## **4.8 Conclusion** 

This chapter defines the complete Layer 2 segmentation for the switching layer. All VLANs are created according to the network design, access ports are assigned to their correct user segments, and trunk links are configured to forward only the required tagged traffic across the topology. 

The switching infrastructure now uses a consistent VLAN database, correct port roles, and properly restricted trunk paths.  
The Layer 2 topology is fully prepared for the implementation of inter-VLAN routing in the next chapter.


---

<br>

**Next chapter:** [Inter-VLAN Routing](05-inter-vlan-routing.md)
