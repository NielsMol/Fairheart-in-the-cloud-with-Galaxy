### reference data with CMFV
#### sources
https://training.galaxyproject.org/training-material/topics/admin/tutorials/cvmfs/tutorial.html


Add this tois requirement.yml
- src: galaxyproject.cvmfs
  version: 0.2.8
  
ansible-galaxy role install -p roles -r requirements.yml


cd group_vars
nano galaxyservers.yml

add this :
# CVMFS vars
cvmfs_role: client
galaxy_cvmfs_repos_enabled: config-repo
cvmfs_quota_limit: 500


nano galaxy.yml
add this to doc:

- hosts: galaxyservers
  become: true
  roles:
    # ... existing roles ...
    - galaxyproject.cvmfs
	
ansible-playbook galaxy.yml


add the following to galaxyservers.yml:
galaxy_config:
  galaxy:
    # ... existing configuration options in the `galaxy` section ...
    tool_data_table_config_path:
      - /cvmfs/data.galaxyproject.org/byhand/location/tool_data_table_conf.xml
      - /cvmfs/data.galaxyproject.org/managed/location/tool_data_table_conf.xml
	
