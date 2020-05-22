### galaxy monitoring with reports

### note

THIS ISNT WORKING YET\
also its very nice to monitor your galaxy instance





``cd templates/galaxy/config/object_store_conf``\
``nano reports.yml``

add the following:

	uwsgi:
	    socket: 127.0.0.1:9001
	    buffer-size: 16384
	    processes: 1
	    threads: 4
	    offload-threads: 2
	    static-map: /static/style={{ galaxy_server_dir }}/static/style/blue
	    static-map: /static={{ galaxy_server_dir }}/static
	    static-map: /favicon.ico=static/favicon.ico
	    master: true
	    virtualenv: {{ galaxy_venv_dir }}
	    pythonpath: {{ galaxy_server_dir }}/lib
	    mount: /reports=galaxy.webapps.reports.buildapp:uwsgi_app()
	    manage-script-name: true
	    thunder-lock: false
	    die-on-term: true
	    hook-master-start: unix_signal:2 gracefully_kill_them_all
	    hook-master-start: unix_signal:15 gracefully_kill_them_all
	    py-call-osafterfork: true
	    enable-threads: true
	reports:
	    cookie-path: /reports
	    database_connection: "postgresql:///galaxy?host=/var/run/postgresql"
	    file_path: /data
	    filter-with: proxy-prefix
	    template_cache_path: "{{ galaxy_mutable_data_dir }}/compiled_templates"
	
edit galaxyservers:
``nano galaxyservers.yml``

add the following: 

	galaxy_config_templates:
	...
	  - src: templates/galaxy/config/reports.yml
	    dest: "{{ galaxy_config_dir }}/reports.yml"


add at systemd on top level:
``galaxy_systemd_reports: true``

add at NGINX the following:

	location /reports/ {
	    uwsgi_pass           127.0.0.1:9001;
	    uwsgi_param          UWSGI_SCHEME $scheme;
	    include              uwsgi_params;
	}

	######################################
	### Syntax error wel ask helena######
	########################################

### Galaxy monitoring with telegraf and Grafana


#### setting up influxDB
edit requirement.yml
``nano requirement.yml``

	add the following:
	- src: usegalaxy_eu.influxdb
	  version: v6.0.3
	  
  install the new items in requirement
``ansible-galaxy install -p roles -r requirements.yml``

Create a new playbook called monitoring.yml

``nano monitoring.yml``\
add the following:

	---
	- hosts: monitoring
	  become: true
	  roles:
	    - usegalaxy_eu.influxdb
	
Add the fowloing to the hosts file:
``nano hosts``

	[monitoring]
	training-0.example.org ansible_connection=local

``ansible-playbook monitoring.yml``

####  note
you will get an error but that is becouse he installed twice and he cant handle that. it still works those


set the port 8086 open:\
``sudo ufw allow 8086``

	$ influx
	Connected to http://localhost:8086 version 1.7.7
	InfluxDB shell version: 1.7.7
	> show databases
	name: databases
	name
	----
	_internal
	> use _internal
	Using database _internal
	> show measurements
	name: measurements
	name
	----
	cq
	database
	httpd
	queryExecutor
	runtime
	shard
	subscriber
	tsm1_cache
	tsm1_engine
	tsm1_filestore
	tsm1_wal
	write


#### Grafana

add the following to requirements.yml\
``nano requirements.yml``
	- src: cloudalchemy.grafana
	  version: 0.14.2
  
 ``ansible - galaxy install -p roles -r requirements.yml``

add cloudalchemy.grafana to your monitoring.yml

#### note
Password and username should be the same for authenthication

Create a new file  ingroup_vars also called monitoring.yml
add the following:

		grafana_url: "https:///grafana/"

		grafana_security:
		    # Please change at least the password to something more suitable
		    admin_user: admin
		    admin_password: password

These datasources will be automatically included into Grafana

		grafana_datasources:
		 - name: Galaxy
		   type: influxdb
		   access: proxy
		   url: http://127.0.0.1:8086
		   isDefault: true
		   version: 1
		   editable: false
		   database: telegraf

``ansible-playbook monitoring.yml``

update the follownig templates/nginx/galaxy.j2 file\
Add this:

		    ...
		    location /grafana/ {
			proxy_pass http://127.0.0.1:3000/;
		    }
	
set port 3000 open:
``sudo ufw allow 3000``

#### configuring telegraph

edit requirements.yml\
``nano requirements.yml``

	- src: dj-wasabi.telegraf
	  version: 0.12.0
  
  install the new requirements
  ``ansible-galaxy install -p roles -r requirements.yml``
 
 
 ADD THE  following role to galaxy.yml
 ``nano galaxy.yml``

	 - dj-wasabi.telegraf
 
 create the following file group_vars/all.yml 
 ``nano group_vars/all.yml``
 
 add the folowing:
 
	 # Install the latest version
	telegraf_agent_package_state: latest

	# Configure the output to point to an InfluxDB
	# running on localhost, and # place data in the
	# database "telegraf" which will be created if need be.
	telegraf_agent_output:
	  - type: influxdb
	    config:
	    - urls = ["http://127.0.0.1:8086"]
	    - database = "telegraf"

	# The default plugins, applied to any telegraf-configured host
	telegraf_plugins_default:
	  - plugin: cpu
	  - plugin: disk
	  - plugin: kernel
	  - plugin: processes
	  - plugin: io
	  - plugin: mem
	  - plugin: system
	  - plugin: swap
	  - plugin: net
	  - plugin: netstat
  
  add the ffollowing to galaxyservers.yml
  ``nano galaxyservers.yml``
  
	telegraf_plugins_extra:
	  listen_galaxy_routes:
	    plugin: "statsd"
	    config:
	      - service_address = ":8125"
	      - metric_separator = "."
	      - allowed_pending_messages = 10000

Add the folowing to galaxy_config block:

	galaxy_config:
	  galaxy:
	    ...
	    statsd_host: localhost
	    statsd_influxdb: true
	    ...

    
 
#### sources
https://training.galaxyproject.org/training-material/topics/admin/tutorials/reports/tutorial.html


https://training.galaxyproject.org/training-material/topics/admin/tutorials/monitoring/tutorial.html
