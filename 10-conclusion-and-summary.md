# **10 - Conclusion and Summary**

<br>

## **Final Evaluation**

This first GNS3 project describes a complete small office network that uses VLAN segmentation, static routing with _ip route_, inter-VLAN routing, DHCP services, NAT/PAT and security rules. All parts of the configuration follow the design, and the network operates as one functional system.

The topology includes Router R1, an ISP router, three switches, a Windows Server, a Windows-Admin workstation, and several user devices (VPCS and one Windows inter-user PC). The addressing plan uses simple and clear subnetting, which is suitable for a first project with a smaller infrastructure.

Inter-VLAN routing is configured using subinterfaces with 802.1Q encapsulation. DHCP assigns IPv4 addresses dynamically, and the Windows Server provides services for management and testing. The Windows-Admin workstation acts as the main administrative device with full access to all network segments.

NAT/PAT translates internal addresses to a public address on the ISP-facing interface. The Internet is simulated communication “out” goes through the ISP router, which represents the external network. When an internal device communicates with an external host, the source address is translated to the router’s public address.

Static routing completes the communication path. R1 uses a default route toward the ISP, and the ISP router returns all internal networks back to R1 using _ip route_.

ACLs control the communication between VLAN segments. Admin VLANs have full access, user VLANs stay isolated, and all segments are allowed to reach the server and the simulated Internet. This creates a simple and clear communication policy that follows the design of a small office network.

Troubleshooting confirms the correct operation of VLANs, trunks, routing, DHCP, NAT/PAT and ACLs after fixing the issues found during the project. Diagnostics verify forwarding, address translation, VLAN assignment and ACL application. The network is stable, consistent and follows the communication policy defined in the design.

The network is fully built with all planned functions. It forms a complete small office model that provides a solid base for more complex projects, multi-layer infrastructure and advanced routing protocols in future projects. :))

<br>

## **Project Overview**


1. **Topology Design**: The network uses Router R1, an ISP router, three switches, a Windows Server, a Windows-Admin workstation and several user devices. All devices are placed in separate VLAN segments.

2. **Addressing and VLAN Plan**: Each VLAN receives a dedicated IPv4 subnet. The addressing plan defines gateways, DHCP scope and the point-to-point link toward the ISP.

3. Basic Device Configuration**: Hostnames, static addressing and basic connectivity are configured. Windows Server and Windows-Admin form the management part of the network.

4. **VLANs and Trunking**: All switches create VLANs and assign access ports. Trunk ports carry VLANs across the switching layer two.

5. **Inter-VLAN Routing**: Router R1 uses 802.1Q subinterfaces and provides gateway addresses for each segment.

6. **DHCP Services**: Windows Server assigns DHCP addresses. R1 forwards DHCP requests from all VLANs using relay.

7. **NAT/PAT and Static Routing**: R1 uses a default route to the ISP. The ISP router returns all internal networks with _ip route_. NAT/PAT translates internal addresses to the public address of R1.

8. **Network Security**: Device protection, port security, SSH and ACL policies define the security structure. Admin VLANs have full access; user VLANs stay isolated.

9. **Troubleshooting**: Real issues with VLANs, trunk links, NAT/PAT and ACL order are found, corrected and verified. Diagnostics confirm full network stability.


---

<br>

**Back to project overview:** 