# InterVLAN-L3 
The next step from VLANS, inter VLAN routing
Cisco Packet Tracer lab exploring building inter vlan routing, using a layer 3 switch
<br clear="left" />

<img width="400" height="400" alt="image" align="left" src="https://github.com/user-attachments/assets/9ffc8ac7-3831-475e-8861-6a104f37e3cc" />

### 📌 Project Objective

The objective of this lab is to develop my understanding of inter VLAN routing using a layer 3 switch. There was the option to only use the router, but the layer 3 switch was a more viable option due to it's scalability.

### 🛠️ Hardware 

- **Switches:** 1x 3560 24PS , 1x Cisco Catalyst 2960 (SW1)
- **Hosts:** 7x End Devices (PCs)
- **Router** 2911 router

I was simulating a real office scenario, i i thought that an IT department would have alot more laptops due to support and programming 
Hence  why i put 4 laptops.
<br clear="left" />


## Questions I had while building

1. Why couldn't i use one switch for the set up? I have a newfound knowledge of VLANs and it woud allow me to put multiple devices on the same switch while keeping the information seperate. Adding another switch seems like using extra hardware unessecarily. ***I think that for a lab, the one switch architecture would be fine, but my aim in this lab is also to simulate a real life enterprise network. In a real world enterprise lab, each floor/wing would ideally get its own access switch, and I am dealing with 2 parts of a buisness IT and HR.***

2. Why do i need to configure vlans as interfaces?I am confused how the layer 3 switch can have two ip addresses. ***I had to come to understand that it was the logical SV interfaces inside the Layer 3 switch that are acting as the default gateways, not the physical. I was so used to assigning Ip addresses to physical things that it was difficult to conceptualise at first. Without the vlan interfaces, D1 has no layer 3 presence to route data between the VLANs at the network layer***

3. WHat is stopping the layer 3 switch from handing out the incorrect DHCP pool? How would it know? i am confused, because it seems in the code for dhcp, the vlan 5 part is string. Im curious as to how the switch would know ? does it only assign DHCP info based on ip address assigned to vlan interface? **It seems that the DHCP pool name is indeed just string, just a name for me. The only thing that the L3 switch uses is the SVI that the data arrives on, so it assignes the data based on that**

4. Why do i need to make the connection between the switches and the layer 3 switch as a turnk port? I thought the trunk port was only for when you are carrying two types of tagged traffic? ***I think this is one instance of a trunk port being used, but the ports connecting the switches to the layer 3 switch need to be ttrunk ports because the L3 switch needs to know which traffic belongs to which VLAN. If i keep the ports as access ports, then the tags will be stripped, which will leave L3 switch int he dark and will assing the wrong ip addresses to devices***

5. What is dot1q? Why did i have to enter a command before enabling trunking on the layer 3 switch? ***dot1q (802.1Q) is the standard protocol that defines how VLAN tags are added to Ethernet frames.That tag is what tells the receiving switch which VLAN the frame belongs to.
The reason the 3560 asks you to specify it is because older Cisco switches also supported another trunking protocol called ISL — which wrapped the entire frame instead of inserting a tag. dot1q is now the most used, but the 3560 still supports both so you have to tell it which one to use***

## 💻 Cisco IOS Configuration Steps
Here are the commands i entered 

### 1. Enabled the access ports  (S1 & S2)
```text
S1> enable
S1# configure terminal
S1(config)# interface range FastEthernet 0/1 - 3
S1(config-if-range)# switchport mode access
S1(config-if-range)# exit
S1(config)# exit
S1# write memory
```
This was done for both switches

### 2. Enabled vlans 5 and 10 and assigned ports to vlans - vlan 5 for SW-IT and vlan 10 for SW-HR (S1 & S2)
```text
S1> enable
S1# configure terminal
S1(config)# vlan 5
S1(config-vlan)# name HR
S1(config-vlan)# exit
S1(config)# interface range FastEthernet 0/1 - 3
S1(config-if-range)# switchport access vlan 5
```

### 3. Enabled the switch output ports connected to layer 3 switch as trunk ports, to carry tagged traffic to L3 switch (S1 & S2)
```text
S1> en
S1# conf t
S1(config)# interface Gig0/1
S1(config-if)# switchport mode trunk
```

### 4. Attempted to enable the input layer 3 switch interfaces as trunks, so that the layer 3 switch could recieve the Vlan id tagged data from 5 and 10.

I then attempted to make D1 input ports as trunk ports, but the system didn't let me!? I kept on getting an error message.
<img width="753" height="146" alt="image" src="https://github.com/user-attachments/assets/9db80f39-e74f-4c27-accb-05c5842b1ad0" />

After researching, i found out that unlike the 2960 layer 2 switches that only support dot1q,  the layer 3 3560 supports multiple trunking protocols. So before enabling port as trunk, i have to specify which protocol i want. So following this realisation, I specificed the dot1q protocol, then I was able to enable the interfaces as trunks.

```text

S1(config)# interface FastEthernet0/1
S1(config-if)# switchport trunk encapsulation dot1q
S1(config-if)# switchport mode trunk

```
### 5. Enabled the Vlans as virtual interfaces on the switch, to act as default gateways to route between VLANs at layer 3

```text
Switch(config)#interface vlan 5
Switch(config-if) #ip address 192.168.9.1
Switch(config-if) #ip address 192.168.9.1 255.255.255.0
Switch(config-if) #no shutdown
Switch(config-if) #exit
Switch(config) #interface vlan 10
Switch(config-if) #ip address 192.168.13.1
Switch(config-if) #ip address 192.168.13.1 255.255.255.0
Switch(config-if) #no shutdown
Switch(config-if) #exit
```

The no shutdown command pushed the interfaces into an up state. Note how the default gateways where assigned ip addresses in the same subnet as the other devices on their vlan. 

### 6. Configured a DHCP pool on the layer 3 switch, to allow assigning of ip addresses to hosts based on default gateway ip address network

```
Switch(config)#ip dhcp pool VLAN5
Switch(dhcp-config)#network 192.168.9.0 255.255.255.0
Switch(dhcp-config)#default-router 192.168.9.1
Switch(dhcp-config)#exit
Switch(config)#exit
Switch#write memory

Switch#conf t
Switch(config)#ip dhcp pool VLAN10
Switch(dhcp-config)#network 192.168.13.0 255.255.255.0
Switch(dhcp-config)#default-router 192.169.13.1
Switch(dhcp-config)#end
Switch#conf t
Switch(config)#ip routing
Switch(config)#exit
Switch#write memory

```

The `ip routing` command at the end, enables layer 3 routing on the switch, without it, the layer 3 switch is no different from the 2960s, in that it doesn't route on layer 3.

### 7. Switched the PC ip confguraitons in the PC settings menu to DHCP, allowing the Ip addresses to be automatically assigned 

<img width="704" height="268" alt="image" src="https://github.com/user-attachments/assets/eedbe822-33ca-4b32-a5da-6d24465be238" />

### 8. Pinged Device on another vlan from a device in one VLAN


<img width="475" height="208" alt="image" src="https://github.com/user-attachments/assets/7ba551bb-af9f-4469-9a81-7abb26c87eaa" />

I pinged devices on the same VLAN and devices on other VLANs, the pings where succesful.

## :chart_with_upwards_trend: Learning
- From this lab i learnt how layer 3 switches can allow for intervaln routing, allowing people on differetn VLANs to communicate with each other
- I kearnt how helpful dhcp pooling can be to assign the right ip addresses to pcs, without haivng to manually type 1 for every one.
- I also learnt about dot1q.
- I also was remmnded that if the vlans are not enbaled as SVIs, there can be no layer 3 routing. THe layer 3 switch will still act as a layer 2 switch.

