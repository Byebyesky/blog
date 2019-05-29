---
layout: "post"
title:  "Hacking a router 03: The serial interface"
date:   2019-05-28 09:54:24 +0200
categories: hacking embedded
---


## Booting
When we connect to the serial interface and then boot up the router we're greeted with a huge amount of information.
Let's split it up into pieces:

```
Booting...
init_ram
 00000102 W init ddr ok

DRAM Type: DDR2
	DRAM frequency: 533MHz
	DRAM Size: 32MB
JEDEC id C84015, EXT id 0xc840
found gd25q16
flash vendor: GigaDevice
gd25q16, size=2MB, erasesize=4KB, max_speed_hz=41000000Hz
auto_mode=0 addr_width=3 erase_opcode=0x00000020
=>CPU Wake-up interrupt happen! GISR=89000004 
 ```

This block is the detection of DRAM and flash memory.

 ```
---Realtek RTL8197F boot code at 2018.03.02-14:45+0800 v3.4.11B.9 (600MHz)
Mac addr:b4-0f-3b-24-9b-90
wait for upgrage
port link 0x000030e0 0x000030e0 0x000030e0 0x000030e0
irq:0x00008480
Jump to image start=0x80500000...
decompressing kernel:
Uncompressing... done, booting the kernel.
done decompressing kernel.
start address: 0xa0000600
```
This block seems to establish an connection over the wire and wait for an upgrade (most likely over TFTP). After it's done waiting it starts decompressing and booting the kernel.  
From this we know the initial image is loaded at `0x80500000` and is compressed. And the decrompressed kernel starts at `0xa0000600`, which is an uncached mirror of the DRAM region at `0x80000600`. The base address of the DRAM region is at ``0x80000000/0xa0000000`` and the end is 32MB after ``0x8200000/0xa2000000`` (To find out about what all these addresses mean consult the processor manual).  
With this we know where to find the firmware image in ram, which will come in handy very soon.

```  
[cyg_net_init] Init: mbinit(0x00000000)
[cyg_net_init] Init: cyg_net_init_devs(0x00000000)
Init device 'rltk819x_eth0'
Init device 'rltk819x_eth1'
Init device 'rltk819x_eth7'
Init device 'rltk819x_peth0'
[peth0] added, mapping to [eth1]...
Init device 'rltk819x_eth4'
Init device 'rltk819x_eth3'
Init device 'rltk819x_eth2'
Init device 'rltk819x_wlan1'
RFE TYPE =0
[pwlan0] added...
Init device 'rltk819x_wlan1-vxd'
RFE TYPE =0
Init device 'rltk819x_wlan1-va3'
RFE TYPE =0
Init device 'rltk819x_wlan1-va2'
RFE TYPE =0
Init device 'rltk819x_wlan1-va1'
RFE TYPE =0
Init device 'rltk819x_wlan1-va0'
RFE TYPE =0
Init device 'rltk819x_wlan0'
[pwlan0] added...
Init device 'rltk819x_wlan0-vxd'
Init device 'rltk819x_wlan0-va3'
Init device 'rltk819x_wlan0-va2'
Init device 'rltk819x_wlan0-va1'
Init device 'rltk819x_wlan0-va0'
Init device 'rltk819x_pwlan0'
[cyg_net_init] Init: loopattach(0x00000000)
[cyg_net_init] Init: ifinit(0x00000000)
[cyg_net_init] Init: domaininit(0x00000000)
[cyg_net_init] Init: cyg_net_add_domain(0x80403298)
New domain internet at 0x00000000
IP packet filtering initialized, divert enabled, rule-based forwarding enabled, default to deny, logging disabled
Realtek FastPath:v1.03
[cyg_net_init] Init: cyg_net_add_domain(0x80403148)
New domain route at 0x00000000
[cyg_net_init] Init: call_route_init(0x00000000)
[cyg_net_init] Init: sysctl_register_all(0x00000000)
[cyg_net_init] Done
Creating Init Thread...
cpuload_calibration_value=6070449
Envram is initing! base_addr=0x1c000 size=0x3000
@@@[envram] crc=0x57 org=0x57
Nvram is initing! base_addr=0x1f8000 size=0x7fff
@@@[nvram] crc=0xa8 org=0xa8
System start
Realtek GPIO init
this thread can be checked ! if thread stack overflow or soiled
```

This looks like the boot code for the whole system, which initialises all the peripheral devices.


```
------------main
tenda_ifconfig error 
: Invalid argument
[MAIN->tapf_vlan_config->262]:vlan config ok!
[MAIN->br_init->878]:br(bridge0) init ok!
[MAIN->br_init->878]:br(bridge1) init ok!
[MAIN->init_before_module_init_services->76]:init_before_module_init_services ok!
[DEBUG] [MSG->msg_init->37]:rc_msg init ok!
eth0: promiscuous mode enabled
kMAIrNe-8>6t5axp_fi_ovcltaln(_)c:o4n8f0i0g,- >s2e6t2 ]p:ovrlta n3  c0oMn fFiUgL Lo
 !
[[WARN] Interface not initialized yet!
r[WARN] Interface not initialized yet!
 ->rc_imnsigt_W2e_btsl:v -l>a1n0i8p]=:[k1e9y2 .=1 6o8p.,1v7a8l.u7e7 ]=
 3
0[-M-S-G--->-m-s-g-_-siennidt_Wmesbgs_-2-_-m-b-o-x--->-1-2-0-]2:7c0e-n-t-e-r-:-0-0-0-0-0-1
 6
[MSG->msg_send_msg_2_mbox->136]:send msg [op=3] success!
[TPI->tpi_lan_start->146]:start success!
Interface doesn't accept private ioctl...
clear_acl_table (8B9E): Unknown error
Interface doesn't accept private ioctl...
clear_acl_table (8B9E): Unknown error
wlan0     fflush_acl_list:0  
wlan1     fflush_acl_list:0  
func:ipnat_init i = 0
[TPI->tpi_firewall_start->239]:start success!
_TPI->tpi_http_start->128]:htt#p# #sftuanrct= asuutcoc_ecsosn!n
!eCxLtIe>n d[_TsPtIa-t>utsp_ii_nuiptn,p _ssetta ratu-t>o1-0c7o]n:ns trasrsti  stuoc c-e3s5s

osl_open_udp_socket: bind failed: Can't assign requested address[TPI->tpi_dnsmasq_start->75]:start success!
[TPI->tpi_sntp_start->80]:start success!

[TPI->tpi_dhcp_server_start->323]:start success!
[TPI->tpi_wan_surf_check_start->599]:start success!
pTPI->tpi_wan_mode_check_update_info->75]:mode_need_switch is not yes[!t
ri[_TiPgIm-p>_tipnii_ta]u[t1o2_6c]o[nlnu_msienraviesr]_ sntoatrhti-n>g1 7t3o] :dsot.a
 t success!
[TPI->tpi_reboot_check_start->463]:start success!
[ERROR] [TPI->tpi_wifi_switch_sched_update_info->67]:schedule count[ 0 ] wrong
[ERROR] [TPI->tpi_ddns_start->105]:the mib is off, connot start!
.....ucloud.............do......option....
[TPI->tpi_ucloud_start->101]:start success!


[TPI->tpi_led_sta[WARN] Interface not initialized yet!
r[WARN] Interface not initialized yet!
t->447]:start success!
[TPI->tpi_switch_led_start->118]:start success!
[MAIN->init_after_module_init_services->87]:init_after_module_init_services ok!
[rc->rc_module_msg_handle->157]:start handle module[6,rc_firewall]'s msg,op=3.debug_id=0.wlan_ifname=.string_info=
[TPI->tpi_firewall_action->137]:op=3
ioctl(SIOCIPFFL): Invalid argument
[TPI->tpi_firewall_stop->263]:stop success!
Interface doesn't accept private ioctl...
clear_acl_table (8B9E): Unknown error
Interface doesn't accept private ioctl...
clear_acl_table (8B9E): Unknown error
wlan0     fflush_acl_list:0  
wlan1     fflush_acl_list:0  
func:ipnat_init i = 0
[TPI->tpi_firewall_start->239]:start success!
```

And this one starts the main loop in the kernel.  

Now we see a ``CLI>`` on the screen, which looks like a shell. When we try typing ``help`` we get a bunch of output:  

```
Realtek's command:

Commands    Descriptions                             Usage               
--------    ------------                             -----
db          db <Address> <Len>                                           
dw          dw <Address> <Len>                                           
eth         eth                                                          
wlan0       wlan0                                                        
wlan0-va0   wlan0-va0                                                    
wlan0-va1   wlan0-va1                                                    
wlan0-va2   wlan0-va2                                                    
wlan0-va3   wlan0-va3                                                    
wlan0-vxd   wlan0-vxd                                                    
wlan1       wlan1                                                        
wlan1-va0   wlan1-va0                                                    
wlan1-va1   wlan1-va1                                                    
wlan0-va2   wlan0-va2                                                    
wlan1-va3   wlan1-va3                                                    
wlan1-vxd   wlan1-vxd                                                    
eb          eb <Address> <Value1> <Value2>...                            
ew          ew <Address> <Value1> <Value2>...                            
alg         alg                                                          
brconfig    brconfig                                                     
cat                                                                      
cpuload     cpuload                                                      
date        show system local time                                       
excep       cause exception                                              
fastpath    fastpath                                                     
getmib      getmib                                                       
help        Displays a list of commands                                  
?           Displays a list of commands                                  
hexcat                                                                   
dump        Shows a memory dump                                          
hw_nat      hw_nat                                                       
ib          ib                                                           
idd         idd                                                          
ifconfig    ifconfig                                                     
ipfw        ipfw                                                         
irf         irf                                                          
iw          iw                                                           
iwpriv      iwpriv                                                       
ll                                                                       
mac         mac <ifname> [mac addr]                                      
mroute      mroute                                                       
ob          ob                                                           
od          od                                                           
orf         orf                                                          
ow          ow                                                           
pdump       dump a  thread                                               
ping        ping                                                         
port_fwd    port_fwd                                                     
version     Shows build version                                          
ps          Shows a list of threads                                      
reboot      Reset the system                                             
reinit      reinit                                                       
reinit_test reinit_test                                                  
reset       Reset the system                                             
route       set static route                                             
rssi        rssi                                                         
alias       show alias table                                             
rtl_vlan    rtl_vlan                                                     
sp          Sets a threads priority                  [thread ID]         
setmib      setmib                                                       
show        show                                                         
tcpstats    tcpstats                                                     
test_skb    test_skb                                                     
kill        Kills a running thread                   [thread ID]         
release     Break a thread out of any wait           [thread ID]         
trigger_port trigger_port                                                 
uptime      Shows system uptime                                          
watchdog    on/off/res/reboot                                            

Tenda's command:
msg         ce_power    app_debug   build       stat_link   link_status 
iwpriv      realtek     splx        thread      mbuf        debug       
route       et          wlconf      fw          ping        ifconfig    
syslog      time        tenda_arp   arp         envram      nvram       
restart     reboot 
```

I think ``Realtek's command`` are commands that are provided by the firmware from Realtek and ``Tenda's  command`` are the commands this company added. Let's have a look at the interesting ones:

```
eb          eb <Address> <Value1> <Value2>...
ew          ew <Address> <Value1> <Value2>...  
```
eb/ew writes a sequence of bytes/words to the specified address. That means we can write anywhere from this serial console.

```
dump        Shows a memory dump
```

And with this we have an arbitrary read, that's basically all we need for now, since our goal is to get the firmware off of it for further reverse engineering.  
The flash memory is 2048KB large and the kernel image is compressed. I doubt they're using all of it so we're just going to dump a little over 2MB.
With the addresses that we got from the bootlog we can get the kernel by typing ``dump 0x80000600 2424832`` and logging the output to a file with something like ``tee``. With this we have a file that looks like a hexdump made with ``xxd -g 1`` and we can convert it to a binary with this script:

```python
#!/usr/bin/env python3
#Takes hexdumps (eg. xxd -g 1 [file]) and makes binaries out of them.
import struct
import sys

EXPECTEDADDR = 0
binaryData = b''

if len(sys.argv) > 3:
    BASEADDR = int(sys.argv[3], 16) #Where is it loaded in RAM?
    with open(sys.argv[1]) as k:
        data = k.read()
        data = data.split('\n')   
        for i, line in enumerate(	data):
            try:
                address = line[:8]
                fileAddr = int(address, 16)-BASEADDR
                if(fileAddr != EXPECTEDADDR):
                    raise ValueError
                hexBytes = line[10:57]
                hexBytes = hexBytes.split(' ')
                for byte in hexBytes:
                    binaryData += struct.pack("B", int(byte, 16))
                EXPECTEDADDR += 16
                print(i)
            except ValueError:  #read address is not the one expected
                print("Error in line: ", i)
                exit(1)
    
    binaryData = bytearray(binaryData)
    with open(sys.argv[2], "wb") as out:
        out.write(binaryData)
        print("File has been written to", sys.argv[2])

else:
    print("Usage:", sys.argv[0], "[inputfile] [outputfile] [baseaddress]")
```

And if we're at it let's also dump the bootloader with ``dump 0xB0000000 74880`` and parse it with my script.  
Now that we have the binaries we can start reversing them!  
But we will leave that for the next part of this series.