# VRRP

VRRP is similar to HSRP, both in operation and configuration. The VRRP master is analogous to the HSRP active gateway, while the VRRP backup is analogous to the HSRP standby gateway. A VRRP group has one master device and one or multiple backup devices. A device with the highest priority is the elected master. The priority can be a number between 0 and 255. The priority value 0 has a special meaning—it indicates that the current master has stopped participating in VRRP. This setting is used to trigger backup devices to quickly transition to master without having to wait for the current master to time out.

VRRP differs from HSRP in that it allows you to use the address of one of the physical VRRP group members as a virtual IP address. In this case, the device with the used physical address is a VRRP master whenever it is available.

The master is the only device that sends advertisements (analogous to HSRP hellos). The default advertisement interval is 1 second. The default holdtime is 3 seconds. HSRP, in comparison, has the default hello timer set to 3 seconds and the hold timer to 10 seconds.

se the millisecond timers only when absolutely necessary and with careful consideration and testing. Millisecond values work only under favorable circumstances, and you must be aware that the use of the millisecond timer values restricts VRRP operation to Cisco devices only.

NOTE*: FOR REFERENCE THE TOPOLOGY IS SAME LIKE HSRP* here is just for configuraiton.

https://github.com/deliawolf/HSRP

## VRRP Config
R1 Configuration
```
!
interface GigabitEthernet0/0
 ip address 192.168.1.2 255.255.255.0
 duplex auto
 speed auto
 media-type rj45
 vrrp 1 ip 192.168.1.1
 vrrp 1 priority 200
!
```
R2 Configuration
```
!
interface GigabitEthernet0/0
 ip address 192.168.1.3 255.255.255.0
 duplex auto
 speed auto
 media-type rj45
 vrrp 1 ip 192.168.1.1
!
```
vrrp config only
R1
```
!
interface GigabitEthernet0/0
 vrrp 1 ip 192.168.1.1
 vrrp 1 priority 200
!
```
R2
```
!
interface GigabitEthernet0/0
 vrrp 1 ip 192.168.1.1
!
```
config priority. higher mean more prioritized
```
!
 vrrp 1 priority 200
!
```
config the authentication
```
R1(config-if)# vrrp 1 authentication md5 key-string <MyVRRP>
```
additional configuration
```
Router(config)# interface GigabitEthernet 1/0/0 
Router(config-if)# ip address 10.1.0.2 255.0.0.0 
Router(config-if)# vrrp 1 timers advertise 3 
Router(config-if)# vrrp 1 timers learn 
Router(config-if)# vrrp 100 timers learn
Router(config-if)# no vrrp 100 preempt
```
Tracking the interfaces
```
Router(config)# track 1 interface Serial 0/1 line-protocol
Router(config-track)# exit
Router(config)# interface Ethernet 1/0
Router(config-if)# ip address 10.0.0.2 255.0.0.0
Router(config-if)# vrrp 1 ip 10.0.0.3
Router(config-if)# vrrp 1 priority 120
Router(config-if)# vrrp 1 track 1 decrement 15
```
verifying config
```
Router# show vrrp

Ethernet1/0 - Group 1  
  State is Master  
  Virtual IP address is 10.0.0.3
  Virtual MAC address is 0000.5e00.0101
  Advertisement interval is 1.000 sec
  Preemption is enabled
   min delay is 0.000 sec
  Priority is 105 
   Track object 1 state Down decrement 15
  Master Router is 10.0.0.2 (local), priority is 105 
  Master Advertisement interval is 1.000 sec
  Master Down interval is 3.531 sec
Router# show track

Track 1 
  Interface Serial0/1 line-protocol
  Line protocol is Down (hw down)
   1 change, last change 00:06:53
  Tracked by:
   VRRP Ethernet1/0 1
```

References : https://content.cisco.com/chapter.sjs?uri=/searchable/chapter/content/en/us/td/docs/ios-xml/ios/ipapp_fhrp/configuration/xe-3s/fhp-xe-3s-book/fhp-vrrp.html.xml#GUID-B6FD065C-69CE-4DA5-917C-A9D626D19C2E
