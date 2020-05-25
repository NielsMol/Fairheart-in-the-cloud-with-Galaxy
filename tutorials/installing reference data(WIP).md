## reference data with CMFV

We install this to make reference genome publicly avaible. so no every user will download their own and occupied unneccesary amount of data.

Add this to requirement.yml:\
``nano requirement.yml``

	- src: galaxyproject.cvmfs
	  version: 0.2.8
	  
install the new requirements: \
``ansible-galaxy role install -p roles -r requirements.yml``

edit galaxyservers.yml:\
``nano groupvars/galaxyservers.yml``

add the following:

	# CVMFS vars
	cvmfs_role: client
	galaxy_cvmfs_repos_enabled: config-repo
	cvmfs_quota_limit: 500

edit galaxy.yml\
``nano galaxy.yml``

add the following to galaxy.yml:

	- hosts: galaxyservers
	  become: true
	  roles:
	    # ... existing roles ...
	    - galaxyproject.cvmfs
	



add the following to galaxyservers.yml:\
``nano groupvars/galaxy.yml``

	galaxy_config:
	  galaxy:
	    # ... existing configuration options in the `galaxy` section ...
	    tool_data_table_config_path:
	      - /cvmfs/data.galaxyproject.org/byhand/location/tool_data_table_conf.xml
	      - /cvmfs/data.galaxyproject.org/managed/location/tool_data_table_conf.xml
	      
play the playbook:
``ansible-playbook galaxy.yml``


#### sources
https://training.galaxyproject.org/training-material/topics/admin/tutorials/cvmfs/tutorial.html
