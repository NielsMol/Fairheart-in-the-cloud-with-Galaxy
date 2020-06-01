# Attaching a datadisk to the VM

### TLDR
a created CEPH image of surfsara can be attached by the UI. it is attached but not usable connected to the VM yet


### make an empty data disk
 - Go to surfsara
 - Images and storage
 - Image create image
 	- Give it a name
 	- Type = generic storage datablock
 	- Datastore = 106: ceptj
 	- Set to persistant
 	- Image location = empty disk image
 	- Size = in mb

### Add data disk to the template
- VMS
 - Template
 - Update
 - Storage
	-  green + button
	-  Output data image, select created data storage
	 
#### Mount data disk to template
 Log in on server
 ``sudo su -``
 `` Mkdir /mnt/galaxy-data``\
  `` Mkfs -t xfs /dev/vdb``\
 `` Mount /dev/vdb /mnt/galaxy-data``\
``sudo blkid = lijst van alle data mounts``
		
### Automating of mounting data image
``nano /etc/fstab``
 Write in latest line: ``/dev/vdb /mnt/galaxy-data xfs defaults 0 0``
 
 this enables that everytime you reboot the VM you dont have to remount the datadisk\
it is then automaticly mounted.

#### check use of images
`` sudo su - `` \
`` df -h ``\

check if you see your attached datadisk in the list

### Sources
https://doc.hpccloud.surfsara.nl/create-datablocks  
https://help.ubuntu.com/community/Fstab
