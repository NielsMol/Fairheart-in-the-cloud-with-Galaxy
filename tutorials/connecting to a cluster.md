
### installing on a computer cluster




#### installing slurm

``nano requirerment.yml``

add the following:

	- src: galaxyproject.repos
  	version: 0.0.1
	- src: galaxyproject.slurm
  	version: 0.1.2
	
 install the new required items:
 ``ansible-galaxy install -p roles -r requirements.yml``

Edit the config log
 ``nano slurm.yml``
 
 add this:
 
	 - hosts: galaxyservers
	  become: true
	  vars:
	    slurm_roles: ['controller', 'exec']
	    slurm_nodes:
	    - name: localhost
	      CPUs: 2                              # Here you would need to figure out how many cores your machine has. (Hint, `htop`)
	    slurm_config:
	      FastSchedule: 2                      # Ignore errors if the host actually has cores != 2
	      SelectType: select/cons_res
	      SelectTypeParameters: CR_CPU_Memory  # Allocate individual cores/memory instead of entire node
	  roles:
	    - galaxyproject.repos
	    - galaxyproject.slurm


Run the playbook\
``sudo ansible-playbook slurm.yml``

#### installing slurm-DRMAA

``nano slurm.yml``

Add the following:
 
	   post_tasks:
	    - name: Install slurm-drmaa
	      package:
		name: slurm-drmaa1

run the playbook once more
``ansible-playbook slurm.yml``

#### galaxy and slurm

``nano group_vars/galaxyserver.yml``

add the following to galaxyservers.yml:

	galaxy_systemd_zergling_env: DRMAA_LIBRARY_PATH="/usr/lib/slurm-drmaa/lib/libdrmaa.so.1"


``mkdir -p files/galaxy/config``
so it will be galaxy/files/galaxy/config

``nano galaxy/files/galaxy/configjob_conf.xml``

add the following:

	<job_conf>
	    <plugins workers="4">
		<plugin id="local" type="runner" load="galaxy.jobs.runners.local:LocalJobRunner"/>
	    </plugins>
	    <destinations>
		<destination id="local" runner="local"/>
	    </destinations>
	</job_conf>


 
edit the following items:

--- files/galaxy/config/job_conf.xml.old
+++ files/galaxy/config/job_conf.xml

	@@ -1,8 +1,9 @@
	 <job_conf>
	     <plugins workers="4">
		 <plugin id="local" type="runner" load="galaxy.jobs.runners.local:LocalJobRunner"/>
	+        <plugin id="slurm" type="runner" load="galaxy.jobs.runners.slurm:SlurmJobRunner"/>
	     </plugins>
	-    <destinations>
	+    <destinations default="slurm">
	+        <destination id="slurm" runner="slurm"/>
		 <destination id="local" runner="local"/>
	     </destinations>
	 </job_conf>
 
 tell galaxyproject.galaxy of where the job config should reside in group variables
 ``nano galaxyservers.yml``
 
 add at #galaxy:
 
 	galaxy_job_config_file: "{{ galaxy_config_dir }}/job_conf.xml"

add at #galaxy_config:

	galaxy_config:
	  galaxy:
	    # ... existing configuration options in the `galaxy` section ...
	    job_config_file: "{{ galaxy_job_config_file }}"
	
And then deploy the new config file using the galaxy_config_files var in your group vars:	

	 galaxy_config_files:
	  # ... possible existing config file definitions
	  - src: files/galaxy/config/job_conf.xml
	    dest: "{{ galaxy_job_config_file }}"
 
 run the playbook:\
 ``ansible-playbook galaxy.yml``\
 Follow the logs with ``journalctl -f -u galaxy``



#### sources
https://training.galaxyproject.org/training-material/topics/admin/tutorials/connect-to-compute-cluster/tutorial.html
