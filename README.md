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

I then wanted to devleop a quicker way to assign the defult gateway for multiple pcs at once.So i created a DHCP pool on the alyer 3 switch for the vlan
changed pc ip config to dhcp
<img width="627" height="190" alt="image" src="https://github.com/user-attachments/assets/2c2346b4-ee8f-4cf9-a6f2-bfb68333f333" />


I realised, the layer 3 switch is not going to know which dhcp pool to use for whihc vln. i forgot this, so i went back to assign access mode to output switch ports to layer 3 switch.
<img width="519" height="145" alt="image" src="https://github.com/user-attachments/assets/4bd5c7e6-e26e-427c-ac9d-1d4cdde04384" />
i did this for both



When You come back osaru:
ASSIGN PORTS CONNECTING SWITCHES AND L3 SWITCHES TOGETHER AS TRUNK PORTS

<img width="627" height="212" alt="image" src="https://github.com/user-attachments/assets/63125461-be2e-4341-b47f-a0dd5fb63966" />
i tried to make d1 input portd thst conect d1 to switch as trunk, but it was not letting me. I was confused as to why, byt using netpilot brought me this answer

<img width="911" height="564" alt="image" src="https://github.com/user-attachments/assets/05f52929-82a0-4546-bb24-4e8d79bf2ee6" />
<img width="604" height="286" alt="image" src="https://github.com/user-attachments/assets/07b58160-6b1a-4cb7-8bea-59d18381450a" />
<img width="704" height="268" alt="image" src="https://github.com/user-attachments/assets/eedbe822-33ca-4b32-a5da-6d24465be238" />




SORT OUT DHCP POOLING FOR DIFFERENT VLANS ON L3 SWITCH

Now that vlans where known by layer 3 switch, atrunk was set up and vlans had ip addresses, i could now sort out dhcp pooling so that the correct ip addreses could be assigned to the pcs.
<img width="931" height="138" alt="image" src="https://github.com/user-attachments/assets/e6bddfc5-dedf-49b3-9df8-83a2906a1a80" />

As of right now, the ip address didn't mathc the ip address of the vlan it was on. FOr example, for hr, for one of pcs, their ip address is 169.254.0.blah, so it shows that dhcp pooling needs to be sorted for d1.

<img width="485" height="60" alt="image" src="https://github.com/user-attachments/assets/bd44364f-910c-48c7-9610-4820a35c4d46" />
<img width="506" height="58" alt="image" src="https://github.com/user-attachments/assets/4b8b1f0a-90de-49dd-82d0-3f93453eb01f" />

<img width="549" height="387" alt="image" src="https://github.com/user-attachments/assets/6d246755-05b5-44f6-b6f7-8b677aa7f864" />
<img width="965" height="312" alt="image" src="https://github.com/user-attachments/assets/b32c4b00-0ec6-4b2c-baa9-9cf52b10ba03" />

After setting it all up, i tried to ping pc1 
<img width="475" height="208" alt="image" src="https://github.com/user-attachments/assets/83a05fe1-fd82-4b7b-b637-de68a2f52f23" />


Can see that, it can ping devices in same subnet, but can't ping devices in other subnet,
must be a layer 3 switch issue 
<img width="756" height="122" alt="image" src="https://github.com/user-attachments/assets/d78b3b63-83da-4ceb-a1f5-074f09576834" />
typed shoow ip interface broef on layer 3 switch, to see if interface issue. Saw the trunk port was down, so, typed no shutdwon
<img width="471" height="158" alt="image" src="https://github.com/user-attachments/assets/40cd9e54-b77d-4c18-92e6-f447ae122287" />
checked mac addess table on vlan 5 and vlan 10 switches, it seems packets where eign recevoed, but was still confided why

<img width="471" height="158" alt="image" src="https://github.com/user-attachments/assets/0047f252-f8bd-45b1-8a0c-64acabef2f51" />

I wonder why the dauly gateway is a different netowrk
<img width="444" height="162" alt="image" src="https://github.com/user-attachments/assets/39b415a8-2cf4-4018-8595-311f44e80093" />

Seems it was not configured properly?
I am sure i typed write memory
<img width="529" height="110" alt="image" src="https://github.com/user-attachments/assets/7b2a203d-9a1c-434d-b7aa-1079fbe03673" />

i did, then typed 

<img width="409" height="275" alt="image" src="https://github.com/user-attachments/assets/383a831d-a9ea-4479-b2a0-bb2dbc7eec62" />

after, went to pcs and refreshed by selecting static then dhcp to refresh dhcp config
<img width="655" height="631" alt="image" src="https://github.com/user-attachments/assets/8740aa67-1fcc-4566-80b8-739874b622d3" />

now this had been fixed, ran ping again from pc

<img width="625" height="98" alt="image" src="https://github.com/user-attachments/assets/bc5b964c-f1cd-47c3-9738-4cad548a5979" />

I was confused, so i ran traceroute to track the hops and see if it is evene reaching the routet. I coudl reach other devices in the subnet so it clearly was nto a switch problem

<img width="541" height="159" alt="image" src="https://github.com/user-attachments/assets/faff5108-7486-404d-bf94-fec6727c7d7e" />

SInce it wasn't even being routed, It had to be a layer 3 issue.
I went to the switch and enabled ip routing, a command i forgot to employ, as without it, thw switch will act like a lyer 2 switch.


I than ran traceroute again andthough i did not get the request timeed out message, i got the destination undreachable message.
It hsows that traffic is now getting into d1, but d1 does not knwo what to do with it .

<img width="612" height="138" alt="image" src="https://github.com/user-attachments/assets/62840c66-6acf-469c-9782-279e60b136ac" />

It seems i forgot to create vlans as interfaces, to route on the layer 3 this is essential. 
<img width="713" height="328" alt="image" src="https://github.com/user-attachments/assets/17b79660-49b0-4924-8eaf-aead71e35980" />

All is fine, i was pinging the incoreect ip addres
<img width="539" height="199" alt="image" src="https://github.com/user-attachments/assets/b43684f9-9529-4811-945e-08fc9c9f6044" />

## :chart_with_upwards_trend: Learning
- From this lab i learnt how layer 3 switches can allow for intervaln routing, allowing people on differetn VLANs to communicate with each other
- I kearnt how helpful dhcp pooling can be to assign the right ip addresses to pcs, without haivng to manually type 1 for every one.
- I also learnt about dot1q.
- I also was remmnded that if the vlans are not enbaled as SVIs, there can be no layer 3 routing. THe layer 3 switch will still act as a layer 2 switch.

