## System DD38

#### note
 System DD38 is to manage galaxy by a process handlers
 
 ``nano galaxy.yml``
 
 Add the role:\
 "usegalaxy_eu.galaxy.systemd
 
 ``nano group_vars/galaxyserver.yml``\
 Add the following:\
 
		galaxy_systemd_mode: mule
		galaxy_zergpool_listen_addr: 127.0.0.1:8080
		galaxy_restart_handler_name: "Restart Galaxy"
 
``nano galaxy.yml``
Add handlers: on the same level as Roles and pre tasks !


	handlers:
    		- name: Restart Galaxy
	  		systemD:
	    			name: galaxy
					state: restarted
		
		
run the playbook\
``ansible-playbook galaxy.yml``

after the new playbook ran preform the following command:\
``sudo systemctl status galaxy``

you should see something like this:\

	$ systemctl status galaxy
	● galaxy.service - Galaxy
   	Loaded: loaded (/etc/systemd/system/galaxy.service; enabled; vendor preset: enabled)
   	Active: active (running) since Wed 2019-11-20 17:11:11 UTC; 9s ago
 	Main PID: 20862 (uwsgi)
    	Tasks: 13 (limit: 4915)
   	Memory: 271.5M (limit: 32.0G)
      	CPU: 9.939s
   	CGroup: /system.slice/galaxy.service
        	   ├─20862 /srv/galaxy/venv/bin/uwsgi --yaml /srv/galaxy/config/galaxy.yml --stats 127.0.0.1:4010
        	   ├─20897 /srv/galaxy/venv/bin/uwsgi --yaml /srv/galaxy/config/galaxy.yml --stats 127.0.0.1:4010
        	   ├─20903 /srv/galaxy/venv/bin/uwsgi --yaml /srv/galaxy/config/galaxy.yml --stats 127.0.0.1:4010
        	   └─20907 /srv/galaxy/venv/bin/uwsgi --yaml /srv/galaxy/config/galaxy.yml --stats 127.0.0.1:4010
		   
		   
	Galaxy restart = systemctl restart galaxy
