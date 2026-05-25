# InterVLAN-ROAS 
The next step from VLANS, inter VLAN routing
Cisco Packet Tracer lab exploring building inter vlan routing, using a layer 3 switch

## 📌 Project Objective
The objective of this lab is to develop my understanding of inter VLAN routing using a layer 3 switch. There was the option to only use the router, but the layer 3 switch was a more viable option due to it's scalability.

## 🛠️ Hardware 
<img width="300" height="180" alt="image" src="https://github.com/user-attachments/assets/4c90d342-8499-41b4-b1c9-846a94d31be8" />

- **Switches:** 1x 3560 24PS , 1x Cisco Catalyst 2960 (SW1)
- **Hosts:** 7x End Devices (PCs)
- **Router** 2911 router

I was simulating a real office scenario, i i thought that an IT department would have alot more laptops due to support and programming 
Hence  why i put 4 laptops.

## Questions I had while building

1. Why couldn't i use one switch for the set up? I have a newfound knowledge of VLANs and it woud allow me to put multiple devices on the same switch while keeping the information seperate. Adding another switch seems like using extra hardware unessecarily. ***I think that for a lab, the one switch architecture would be fine, but my aim in this lab is also to simulate a real life enterprise network. In a real world enterprise lab, each floor/wing would ideally get its own access switch, and I am dealing with 2 parts of a buisness IT and HR.***

2. Why do i need to configure vlans as interfaces?I am confused how the layer 3 switch can have two ip addresses. ***I had to come to understand that it was the logical SV interfaces inside the Layer 3 switch that are actign as the default gateways, not the physcial. I was so used to assigning Ip addresses to physical things that it was difficult to conceptualise at first***

3. WHat is stopping the layer 3 switch from handing out the incorrect DHCP pool? How would it know? i am confused, because it seems in the code for dhcp, the vlan 5 part is string. Im curiois how the switch would know ? does it only assign it based in ip address assigned to vlan interface? ***Since in this labe

4. Why do i need to make the connection between the switches and the layer 3 switch as a turnk port? I thought the trunk port was only for when you are carrying two types of tagged traffic? ***I think this is one instance of a trunk port being used, but the ports connecting the switches to the layer 3 switch need to be ttrunk ports because the L3 switch needs to know which traffic belongs to which VLAN. If i keep the ports as access ports, then the tags will be stripped, which will leave L3 switch int he dark and will assing the wrong ip addresses to devices***

5. What is dot1q? Why did i have to enter a command before enabling trunking on the layer 3 switch? ***dot1q (802.1Q) is the standard protocol that defines how VLAN tags are added to Ethernet frames.
When a frame needs to travel across a trunk link, dot1q inserts a 4 byte tag into the frame containing:
text
Copy
| Destination MAC | Source MAC | 802.1Q Tag | Type | Data |
                               ↑
                          VLAN ID lives here (e.g. VLAN 5)
That tag is what tells the receiving switch which VLAN the frame belongs to.
It's a standard — meaning all vendors (Cisco, HP, Juniper) agree on the same tagging format. That's why switches from different manufacturers can trunk with each other.
The reason the 3560 asks you to specify it is because older Cisco switches also supported a Cisco proprietary trunking protocol called ISL — which wrapped the entire frame instead of inserting a tag. dot1q won and ISL is now obsolete, but the 3560 still supports both so you have to tell it which one to use.**

<img width="487" height="135" alt="image" src="https://github.com/user-attachments/assets/4c4e60bb-62f2-40cf-9242-6a9702d844ca" />
<img width="631" height="179" alt="image" src="https://github.com/user-attachments/assets/cb6cd994-0a4b-43ad-9f68-afd48592065d" />
added vlan to 

<img width="599" height="155" alt="image" src="https://github.com/user-attachments/assets/13344852-8c8a-40d6-9d6f-b1c5ac9114e8" />
<img width="562" height="106" alt="image" src="https://github.com/user-attachments/assets/84a240ff-33b5-4639-84e2-db193a775639" />

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





## :chart_with_upwards_trend: Learning
- From this lab i learnt how layer 3 switches can allow for intervaln routing, allowing people on differetn VLANs to communicate with each other
- I kearnt how helpful dhcp pooling can be to assign the right ip addresses to pcs, without haivng to manually type 1 for every one.
- I also learnt about dot1q.

