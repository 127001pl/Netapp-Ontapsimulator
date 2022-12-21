#
# Instruction on how to successfully run an ONTAP simulator on Proxmox.

#### Index:
- [Overview](#overview)
- [Prerequisites](#prerequisites)
- [Instruction](#instruction)
- [Very Important Notes](#very-important-notes)

----

## Overview:
As I wasn't able to find a correct configuration of the Proxmox VM which allows running the ONTAP simulator without any issues, I prepared my own instruction which was tested a few times. 
All imported ONTAP virtual machines work without any issues with my configuration. 

Didn't describe here how to install, configure and manage a Proxmox server and how to configure ONTAPsimulator. Both of them have their own documentation and support.

## Prerequisites:
1. ONTAPsimulator version: 9.10.1
2. Proxmox version: Virtual Environment 7.3-1
3. DHCP server - Network interfaces of the ONTAP are bridged to the interface of the Proxmox server.

## Instruction:
1. Download the ONTAPsimulator .ova file: https://mysupport.netapp.com/site/tools/tool-eula/simulate-ontap and a .txt file with license keys.
2. Unzip the .ova file
3. Upload to the working directory on the Proxmox server all .vmdk files: 

![image](https://user-images.githubusercontent.com/115875629/208489743-128dddcb-e640-4a71-80e4-edeb286c296b.png)

4. Create a virtual machine with settings shown on screenshots:

![image](https://user-images.githubusercontent.com/115875629/208490420-a41dff11-6433-460a-aee2-617cef774f6b.png)

![image](https://user-images.githubusercontent.com/115875629/208490618-1e65522a-b466-4a83-a28d-ebf5bb651a85.png)

![image](https://user-images.githubusercontent.com/115875629/208490733-813ddad6-8b17-498f-b8e9-9a80914868d4.png)

5. Delete created by default disk and leave it without disks:
  
![image](https://user-images.githubusercontent.com/115875629/208490976-3a62d297-328d-48ca-a764-2b48887753a8.png)

6. Change the amount of the Cores to two and set the Type to SandyBridge (important!):

![image](https://user-images.githubusercontent.com/115875629/208504451-8a6c0601-beca-45c9-ae2f-72b0576fab0d.png)

7. Set the size of the Memory to 6 GB (needed by this version ONTAPsimulator for one node cluster):
    
![image](https://user-images.githubusercontent.com/115875629/208491455-8522aba5-f1d1-4416-b0e2-810c9b861d4f.png)

8. Configure Network interface:
  
![image](https://user-images.githubusercontent.com/115875629/208491676-859237a5-6953-4724-bf3d-7d9db29f5ab8.png)

9. And Create VM:
  
![image](https://user-images.githubusercontent.com/115875629/208491825-5694d4fd-fb70-43a0-b2af-6e603b3a9740.png)

10.  On the newly created VM go to the Hardware and add two/three additional network interfaces:

![image](https://user-images.githubusercontent.com/115875629/208492311-213ad647-f6f8-44b2-bdb0-3720abb33c53.png)

![image](https://user-images.githubusercontent.com/115875629/208492704-d3be131b-ef8b-4ff3-8ec3-2b214c2fe4da.png)

11. Convert and import all four .vmdk files as disks to the newly created VM (here VM ID is 101 and this ID has to be the same as the provided ID in 'qm disk' command). Please do it one by one to keep the order of original disks:

```
qm disk import 101 vsim-NetAppDOT-simulate-disk1.vmdk local-lvm -format qcow2
```

![image](https://user-images.githubusercontent.com/115875629/208494745-ee642fc1-69df-4e37-9186-f7e1828b80ca.png)

![image](https://user-images.githubusercontent.com/115875629/208497432-1f612eda-1da6-4b69-b625-60fd20efa709.png)

12. The newly imported disk should be visible as an Unused Disk:

![image](https://user-images.githubusercontent.com/115875629/208496366-3ec0508c-2ab7-4b84-916a-5f3aa1bd453a.png)

13. Edit this disk and change Bus/Device to the IDE:

![image](https://user-images.githubusercontent.com/115875629/208496891-14577b0c-c83d-4b59-b6b8-50c0a89e65c8.png)

14. Repeat that import for the rest of the three disks accordingly:
```
qm disk import 101 vsim-NetAppDOT-simulate-disk2.vmdk local-lvm -format qcow2
```
![image](https://user-images.githubusercontent.com/115875629/208497679-8e9c3514-74c7-43e8-b078-05b74ae32249.png)

![image](https://user-images.githubusercontent.com/115875629/208497820-633e21e6-8c17-4677-859e-4d6b5e5495a4.png)
```
qm disk import 101 vsim-NetAppDOT-simulate-disk3.vmdk local-lvm -format qcow2
```
![image](https://user-images.githubusercontent.com/115875629/208497931-c090710d-e2ca-4fe9-8fed-6e438e458de8.png)

![image](https://user-images.githubusercontent.com/115875629/208500744-a8efe069-d595-44af-a6cf-22034df65250.png)
```
qm disk import 101 vsim-NetAppDOT-simulate-disk4.vmdk local-lvm -format qcow2
```
![image](https://user-images.githubusercontent.com/115875629/208498493-c3c114db-dc37-443b-9b3f-c4ab8828db89.png)

![image](https://user-images.githubusercontent.com/115875629/208498671-4d9a4eff-0294-4236-927d-0f3f94260b38.png)

![image](https://user-images.githubusercontent.com/115875629/208500937-257bd5ef-abf5-4f1f-bc64-358d19db4c93.png)

15. As the result you should see all four imported to the VM disks (the CD drive was removed in the meantime for clarity of the view):

![image](https://user-images.githubusercontent.com/115875629/208501288-c7a84cd8-8637-4c83-b21d-bdac127ea172.png)

16. In Options / Boot Order unmark net0 and mark ide0 device: 

![image](https://user-images.githubusercontent.com/115875629/208501601-66c966bd-abe1-47da-a6b8-e7e7693b3c6f.png)

17. Start the new ONTAPsimulator VM, and on the Console, a normal boot process should be visible (example screens):

![image](https://user-images.githubusercontent.com/115875629/208514429-9f8124c3-e7bd-4e82-816f-ff718f0d4fb7.png)

![image](https://user-images.githubusercontent.com/115875629/208514539-ab794bc8-e5ab-41c9-8c65-21cf78ebd188.png)

![image](https://user-images.githubusercontent.com/115875629/208514587-71e9d44b-abcf-4314-84a4-1a276653fc8a.png)

18. At this point, you should have a running ONTAPsimulator VM. 
19. For configuration of the Netapp ONTAPsimulator follow the Netapp documentation: Simulate ONTAP 9.10.1 Installation and Setup Guide

## Very Important Notes
1. Proxmox backup of the ONTAPsimulator (done on a working VM) does not protect the simulator. After the restoration from the backup, the internal database is corrupted and the simulator is not usable.

![image](https://user-images.githubusercontent.com/115875629/208877343-6e64c962-7323-46d4-a899-2689f4b6aef1.png)

After reboot:

![image](https://user-images.githubusercontent.com/115875629/208877560-6fbf7fff-f0cd-4de4-bda3-978a52a13413.png)

The backup of the VM should be done when it is switched off.

2. A single node ONTAP cluster can be switched off from CLI (I did not test it with two node cluster):
```
system node halt
```

![image](https://user-images.githubusercontent.com/115875629/208902035-bca9578f-fa35-4a7f-b620-83be2c7af14e.png)

When the system is waiting for physical power off, a VM with ONTAPsimulator can be safely switched off (via the Stop option in Proxmox).

![image](https://user-images.githubusercontent.com/115875629/208902434-99434e30-4c7d-42ec-9bb1-4c87e37cab9b.png)

3. To be honest, I have to mention, that on VMware Workstation I had to create a new simulator - almost every time when I powered it off. To be sure that switching off the ONTAPsimulator will not destroy it, you can take into account hibernation instead of powering it off. This kind of "stopping" ONTAP allows predicting with a higher probability that nothing will happen with the simulator. I did it many times with success.
