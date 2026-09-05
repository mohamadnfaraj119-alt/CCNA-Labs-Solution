====================================================================
LAB 07: LAYER 2 ETHERCHANNEL CONFIGURATION VIA LACP
====================================================================

[Lab Scenario]:
- Problem: In enterprise production networks, connecting switches using a single physical cable creates a bandwidth bottleneck and a Single Point of Failure (SPOF) that can bring down network connectivity if the cable fails. However, adding redundant physical links between switches causes Spanning Tree Protocol (STP) to automatically block those redundant ports to prevent switching loops, preventing us from utilizing the extra bandwidth.
- Solution & Goal: Implement Link Aggregation (EtherChannel) using the industry-standard LACP (Link Aggregation Control Protocol - IEEE 802.3ad). This technology bundles multiple physical interfaces into a single logical interface called a Port-Channel. STP treats this Port-Channel as a single logical pipe, keeping all physical links active without blocking any. This achieves link aggregation (doubling bandwidth) while providing dynamic, seamless failover (redundancy) in case a physical link fails.

[Topology & IP Addressing]:
- S1 (Cisco Catalyst 2960 Switch): Connected to S2 via Fa0/1 and Fa0/2.
- S2 (Cisco Catalyst 2960 Switch): Connected to S1 via Fa0/1 and Fa0/2.
- Port-Channel 1: Operates using LACP in Active mode, configured as a Trunk link to pass all VLAN traffic.

====================================================================
Step-by-Step Configuration & Command Explanation
====================================================================

Step 1: Configure Switch 1 (S1) - Bundle Interfaces & Set Trunk Mode
--------------------------------------------------------------------

enable
   └─ [ Explanation ]: Transition from User EXEC mode to Privileged EXEC mode (# Switch).

configure terminal
   └─ [ Explanation ]: Enter Global Configuration mode ((Switch(config#).

hostname S1
   └─ [ Explanation ]: Set the switch hostname to S1.

interface range fastEthernet 0/1 - 2
   └─ [ Explanation ]: Select the interface range Fa0/1 to Fa0/2 to apply commands simultaneously.

channel-group 1 mode active
   └─ [ Explanation ]: Bundle both interfaces into Port-Channel 1 and enable LACP in Active negotiation mode.

exit
   └─ [ Explanation ]: Exit interface range configuration mode.

interface port-channel 1
   └─ [ Explanation ]: Access the logical Port-Channel 1 virtual interface configuration.

switchport mode trunk
   └─ [ Explanation ]: Configure the logical channel as a Trunk link to carry multi-VLAN traffic between switches.

exit
   └─ [ Explanation ]: Exit Port-Channel interface configuration mode.


Step 2: Configure Switch 2 (S2) - Complete LACP Negotiation
--------------------------------------------------------------------

enable
   └─ [ Explanation ]: Transition from User EXEC mode to Privileged EXEC mode (# Switch).

configure terminal
   └─ [ Explanation ]: Enter Global Configuration mode ((Switch(config#).

hostname S2
   └─ [ Explanation ]: Set the switch hostname to S2.

interface range fastEthernet 0/1 - 2
   └─ [ Explanation ]: Select interfaces Fa0/1 and Fa0/2 connected to S1.

channel-group 1 mode active
   └─ [ Explanation ]: Bundle interfaces into Port-Channel 1 using LACP Active mode to negotiate with S1.

exit
   └─ [ Explanation ]: Exit interface range configuration mode.

interface port-channel 1
   └─ [ Explanation ]: Access the logical Port-Channel 1 virtual interface configuration on S2.

switchport mode trunk
   └─ [ Explanation ]: Set the logical channel as a Trunk link to match S1's configuration.

exit
   └─ [ Explanation ]: Exit Port-Channel interface configuration mode.


Step 3: Verification & Save Configuration
--------------------------------------------------------------------

do show etherchannel summary
   └─ [ Explanation ]: Key command to verify EtherChannel status! Confirm Po1 displays (SU) flags (S = Layer 2, U = In Use) and member interfaces display (P) flags (P = Bundled in port-channel).

do show interfaces trunk
   └─ [ Explanation ]: Verify Port-Channel 1 is actively operating in Trunk mode and passing VLANs.

end
   └─ [ Explanation ]: Return to Privileged EXEC mode (#).

copy running-config startup-config
   └─ [ Explanation ]: Save running configuration to NVRAM to ensure persistence across reboots.


Step 4: Verification & Redundancy Test
--------------------------------------------------------------------
1. Observe the Packet Tracer topology: All link indicators between S1 and S2 turn green, confirming STP treats the bundled links as a single logical interface.
2. Use the Delete Tool to remove one of the physical cables between S1 and S2 during active traffic flow.
3. [Expected Result]: Traffic flows seamlessly over the remaining link with zero packet loss, proving dynamic fault tolerance and high availability (Redundancy).
٤.
