# installing galaxy


### change the followng
- changing database connection
- configuration the admin user list
- changing the "brand" 
 
`` nano galaxy.yml``\
 add the following at pre-tasks and roles:

    pre-tasks [ git, make, virtualenv]\
    roles: geerlingguy.pip , galaxyproject.galaxy, uchida.miniconda\

it should look like the following:

    ---
  	- hosts: galaxyservers
	    become: true
	    pre_tasks:
	      - name: Install Dependencies
		package:
		  name: ['git', 'make', 'python3-psycopg2', 'virtualenv']
	    roles:
	      - galaxyproject.postgresql
	      - role: natefoo.postgresql_objects
		become: true
		become_user: postgres
	      - geerlingguy.pip
	      - galaxyproject.galaxy
	      - role: uchida.miniconda
		become: true
		become_user: galaxy\
	  

edit the galaxyserver.yml:\
`` nano group_vars/galaxyserver.yml``

set the following variable on set galaxy config:


	  galaxy_config:
	    galaxy:
	      key: value

	  galaxy_create_user: true
	  galaxy_sepearate_privileges: true
	  galaxy_manage_paths: true
	  galaxy_layout: root-dir
	  galaxy_root: /srv/galaxy
	  galaxy_user: {name: galaxy, shell: /bin/bash}
	  galaxy_config_style: yaml
	  galaxy_force_checkout: true
	  miniconda_prefix: {{ galaxy_tool_dependency_dir }}/_conda
	  miniconda_version: 4.6.14

	  galaxy_config:
	    galaxy:
	    ...
	    uwsgi:
	      http: 0.0.0.0:8080
	      buffer-size: 16384
	      processes: 1
	      threads: 4
	      offload-threads: 2
	      static-map:
		- /static/style={{ galaxy_server_dir }}/static/style/blue
		- /static={{ galaxy_server_dir }}/static
		- /favicon.ico={{ galaxy_server_dir }}/static/favicon.ico
	      static-safe: client/galaxy/images
	      master: true
	      virtualenv: "{{ galaxy_venv_dir }}"
	      pythonpath: "{{ galaxy_server_dir }}/lib"
	      module: galaxy.webapps.galaxy.buildapp:uwsgi_app()
	      thunder-lock: true
	      die-on-term: true
	      hook-master-start:
		- unix_signal:2 gracefully_kill_them_all
		- unix_signal:15 gracefully_kill_them_all
	      py-call-osafterfork: true
	      enable-threads: true
	      mule:
		- lib/galaxy/main.py
		- lib/galaxy/main.py
	      farm: job-handlers:1,2

Templated variables (like admin user):\
    admin_users: {email1,email2,email3243653y983489}
    brand: Avans
    database_connection: (postgresql:///galaxy?host=/var/run/postgresql) #### is zwaar mogelijk fout
    file_path: ~/galaxy/data #### nodig voor later data storage aan te passen
    check_migrate_tools: false
    tool_data_path: /tool-data



``ansible-playbook galaxy.yml``

``cd /srv/galaxy/server``\
``nano galaxy.yml``  to check if config settings seems alright\
srv/galaxy/var/config = the tool-shet





##### optional lounching uswsig by handlers

``sudo -iu galaxy``

cd /srv/galaxy/server\
(. ../venv/bin/activate)\
uswgi --yaml ../config/galaxy.yml


### sources
https://training.galaxyproject.org/training-material/topics/admin/tutorials/ansible-galaxy/tutorial.html#installing-galaxy
https://www.reddit.com/r/ansible/
https://stackoverflow.com/questions/37878067/ansible-provided-hosts-list-is-empty
