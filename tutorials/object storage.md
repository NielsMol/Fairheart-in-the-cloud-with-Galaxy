
## distributing storage space

When you are running galaxy on a VM where is little tiny space on your precious OS Image. you will not like it that users will flood this with terabytes of data on your (or upgraded 50gb as i reccomand) 2 gb OS image. instead use a CEPH image of TB size

#### note
Check if mounted CEPH image is under /mnt/name_datadisk\
change all data2 names to /mnt/name_datadisk\
See md or tutorial on how to add a datadisk to the VM\
later if needed you can upgrade your VM with easy plug and play a brand new big CEPH image\
just be sure to add it to ``/templates.galaxy/config/object_store_conf.xml`` in the right format

##### IMPORTANT
be sure to give the ``/mnt/name_datadisk`` all rights with the command: ``sudo chmod a+rwx /mnt``\
if you dont do this, it seems liks everything is working and going to upload.
But when you check what happends with the job ``journalctl -f -u galaxy`` 
You will see that the job handler dont got the rights to do anything (*but he thinks he is done)*
So the sad lil job will hang somewhere in the void cramping up your little OS image


#### installing ditributed Object store hierachical

Create and install a datablock referd to previous chapters

edit the galaxyservers.yml in groupvars\
``nano galaxyservers.yml``

add the following:

	 galaxy_config:
	  galaxy:
	    object_store_config_file: "{{ galaxy_config_dir }}/object_store_conf.xml"
	
add to the galaxy_config_templates:

	galaxy_config_templates:
	  - src: templates/galaxy/config/object_store_conf.xml
	    dest: "{{ galaxy_config.galaxy.object_store_config_file }}"
	
	
``nano /templates.galaxy/config/object_store_conf.xml``

add the following:

	<?xml version="1.0"?>
	<object_store type="hierarchical">
	    <backends>
		<backend id="newdata" type="disk" order="0">
		    <files_dir path="/mnt/name_datadisk" />
		    <extra_dir type="job_work" path="/mnt/name_datadisk/job_work_dir" />
		</backend>
		<backend id="olddata" type="disk" order="1">
		    <files_dir path="/data" />
		    <extra_dir type="job_work" path="/data/job_work_dir" />
		</backend>
	    </backends>
	</object_store>





#### ditributed Object store to distributed

nano templates/galaxy/config/object_store_conf.xml

replace with :

	<?xml version="1.0"?>
	<object_store type="distributed">
	    <backends>
		<backend id="newdata" type="disk" weight="1">
		    <files_dir path="/mnt/name_datadisk"/>
		    <extra_dir type="job_work" path="/mnt/name_datadisk/job_work_dir"/>
		</backend>
		<backend id="olddata" type="disk" weight="1">
		    <files_dir path="/data"/>
		    <extra_dir type="job_work" path="/data/job_work_dir"/>
		</backend>
	    </backends>
	</object_store>




####  S3 object store
note* use this when you dont attach a disk to your vm but use a storage server or something:
``nano requirements.yml``\
 add the following :
 
	 - src: atosatto.minio
	  version: v1.1.0

install the required items:\
``ansible-galaxy install -p roles -r requiremtns.yml``

``nano galaxyserver.yml``\
edit the following things:

	minio_server_datadirs: ["/minio-test"]
	minio_access_key: "my-access-key"
	minio_secret_key: "my-super-extra-top-secret-key"

``nano galaxy.yml`` and add the mino role before galaxyproject.galaxy

		- atosatto.minio

``nano templates/galaxy/config/object_store_conf.xml``

Edit the following:

	@@ -1,13 +1,21 @@
	 <?xml version="1.0"?>
	- <object_store type="distributed">
	+ <object_store type="hierarchical">
	     <backends>
	-        <backend id="newdata" type="disk" weight="1">
	+        <backend id="newdata" type="disk" order="1">
		     <files_dir path="/data2"/>
		     <extra_dir type="job_work" path="/data2/job_work_dir"/>
		 </backend>
	-        <backend id="olddata" type="disk" weight="1">
	+        <backend id="olddata" type="disk" order="2">
		     <files_dir path="/data"/>
		     <extra_dir type="job_work" path="/data/job_work_dir"/>
		 </backend>
	+        <object_store id="swifty" type="swift" order="0">
	+            <auth access_key="{{ minio_access_key }}" secret_key="{{ minio_secret_key }}" />
	+            <bucket name="galaxy" use_reduced_redundancy="False" max_chunk_size="250"/>
	+            <connection host="127.0.0.1" port="9091" is_secure="False" conn_path="" multipart="True"/>
	+            <cache path="{{ galaxy_mutable_data_dir }}/database/object_store_cache" size="1000" />
	+            <extra_dir type="job_work" path="{{ galaxy_mutable_data_dir }}/database/job_working_directory_swift"/>
	+            <extra_dir type="temp" path="{{ galaxy_mutable_data_dir }}/database/tmp_swift"/>
	+        </object_store>
	     </backends>
	 </object_store>



#### sources

https://training.galaxyproject.org/training-material/topics/admin/tutorials/object-store/tutorial.html
