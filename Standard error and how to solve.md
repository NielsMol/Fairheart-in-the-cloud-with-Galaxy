## Standard errors and how to solve them

### When logging into the clean new VM

#### error message 

        @@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@
        @    WARNING: REMOTE HOST IDENTIFICATION HAS CHANGED!     @
        @@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@
        IT IS POSSIBLE THAT SOMEONE IS DOING SOMETHING NASTY!
        Someone could be eavesdropping on you right now (man-in-the-middle attack)!
        It is also possible that a host key has just been changed.
        The fingerprint for the ECDSA key sent by the remote host is
        SHA256:I1DqcvtetCqoQp7z8tHWmZjYariA7MzUHYHZC1PFRls.
        Please contact your system administrator.
        Add correct host key in /c/Users/DVrin/.ssh/known_hosts to get rid of this message.
        Offending ECDSA key in /c/Users/DVrin/.ssh/known_hosts:2
        ECDSA host key for 145.100.58.160 has changed and you have requested strict checking.
        Host key verification failed.

##### how to solve

fill in the following command in youw own computer (not the linux vm) \
``"nano .ssh/known_hosts``\
Then remove all entries


### viruantenv 
#### error message

  File "/tmp/galaxy-virtualenv-mBUbpV/virtualenv-16.7.9/virtualenv.py", line 24, in <module>
      import distutils.spawn
  ModuleNotFoundError: No module named 'distutils.spawn'


##### how to solve

``sudo apt install python3-distutils -y and run again``

Check weither python user a clean interpenter\
``sudo apt install python-pip``\
``virtualenv --no-site-packages galaxy_env``\
``. ./galaxy_env/bin/activate``\
``sh run.sh``


### proFTP service dont start
#### error message

  Failed to restart proftpd.service: The name org.freedesktop.PolicyKit1 was not provided by any .service files

#### how to solve
``sudo apt install policykit-1``

### Database connection fail with createuser

######## error message
  createuser: could not connect to database postgres: FATAL:  role "ubuntu" does not exist
  
#### how to solve
``sudo -u postgres -i``

### galaxy ansible playbook fail at start

#### error message

  fatal: [training-0.example.org]: FAILED! => {"msg": "The task includes an option with an undefined variable. The error was: 'galaxy_server_dir' is undefined\n\nThe error appears to be in '/home/ubuntu/galaxy1/roles/galaxyproject.galaxy/tasks/layout.yml': line 7, column 3, but may\nbe elsewhere in the file depending on the exact syntax problem.\n\nThe offending line appears to be:\n\n\n- name: Set any unset variables from layout defaults\n  ^ here\n"}


#### how to solve


make sure the directory is like this:

    .
    ├── ansible.cfg
    ├── galaxy.yml
    ├── group_vars
    │   └── galaxyservers.yml
    ├── hosts
    ├── requirements.yml
    └── roles
        ├── galaxyproject.galaxy
        │   ├── defaults
        │   │   └── main.yml

    we produced this error when it was:

    ├── group_vars
    │   ├── ansible.cfg
    │   ├── galaxy1.yml
    │   ├── galaxyservers.yml
    │   ├── galaxyservers.yml.save
    │   ├── galaxy.yml
    │   ├── hosts
    │   └── roles
    │       ├── galaxyproject.galaxy
    │       │   ├── defaults
    │       │   │   └── main.yml
    │       │   ├── files
### permission fail in ansible

#### error message

  TASK [galaxyproject.galaxy : Instantiate mutable configuration files] **********
  fatal: [145.100.59.212]: FAILED! => {"msg": "Failed to set permissions on the temporary files Ansible needs to create when becoming an unprivileged user (rc: 1, err: chown: changing ownership of '/var/tmp/ansible-tmp-1588234835.46-6168-232989283009378/': Operation not permitted\nchown: changing ownership of '/var/tmp/ansible-tmp-1588234835.46-6168-232989283009378/source': Operation not permitted\n}). For information on working around this, see https://docs.ansible.com/ansible/become.html#becoming-an-unprivileged-user"}


#### how to solve

``nano Ansible.cfg``\

write in the [default]
allow_world_readable_tmpfiles=true

### bzip fail

#### error message

    fatal: [145.100.59.212]: FAILED! => {"changed": false, "msg": "'/usr/bin/apt-mark manual bzip2'
     failed: E: Could not create temporary file for /var/lib/apt/extended_states - mkstemp
     (13: Permission denied)\nE:
     Failed to write temporary StateFile /var/lib/apt/extended_states\n",
     "rc": 100, "stderr": "E: Could not create temporary file for /var/lib/apt/extended_states - mkstemp
     (13: Permission denied)\nE: Failed to write temporary StateFile /var/lib/apt/extended_states\n", "stderr_lines": 
     ["E: Could not create temporary file for /var/lib/apt/extended_states - mkstemp (13: Permission denied)", 
     "E: Failed to write temporary StateFile /var/lib/apt/extended_states"], "stdout": "bzip2 set to manually installed.\n"
     , "stdout_lines": ["bzip2 set to manually installed."]}

#### how to solve

``Sudo apt-get install bzip2``\




### installing Pulsar
#not solved yest

    #### sources
    https://training.galaxyproject.org/training-material/topics/admin/tutorials/pulsar/tutorial.html

    nano requirements.yml
     - src: galaxyproject.pulsar
      version: 1.0.2

    ansible-galaxy install -p roles -r requirements.yml

    cd group_vars
    nano pulsarservers.yml

    add the following:
    pulsar_server_dir: /mnt/pulsar/server
    pulsar_venv_dir: /mnt/pulsar/venv
    pulsar_config_dir: /mnt/pulsar/config
    pulsar_staging_dir: /mnt/pulsar/staging
    pulsar_systemd: true

    pulsar_host: 0.0.0.0
    pulsar_port: 8913

    private_token: your_private_token_here

    pulsar_create_user: true
    pulsar_user: {name: pulsar, shell: /bin/bash}

    pulsar_optional_dependencies:
      - pyOpenSSL
      # For remote transfers initiated on the Pulsar end rather than the Galaxy end
      - pycurl
      # uwsgi used for more robust deployment than paste
      - uwsgi
      # drmaa required if connecting to an external DRM using it.
      - drmaa
      # kombu needed if using a message queue
      - kombu
      # requests and poster using Pulsar remote staging and pycurl is unavailable
      - requests
      # psutil and pylockfile are optional dependencies but can make Pulsar
      # more robust in small ways.
      - psutil

    pulsar_yaml_config:
      dependency_resolvers_config_file: dependency_resolvers_conf.xml
      conda_auto_init: True
      conda_auto_install: True
      staging_directory: "{{ pulsar_staging_dir }}"
      private_token: "{{ private_token }}"

    # NGINX
    nginx_selinux_allow_local_connections: true
    nginx_servers:
      - pulsar-proxy
    nginx_enable_default_server: false
    nginx_conf_http:
      client_max_body_size: 5g

    change the privatecode  to something long string

    Make a new VM

    Add the folowing lines tohosts file:
    [pulsarservers]
    <ip_address of your pulsar server of the VM>

    nano templates templates/nginx/pulsar-proxy.j2
    server {
        # Listen on 80, you should secure your server better :)
        listen 80 default_server;
        listen [::]:80 default_server;

        location / {
            proxy_redirect off;
            proxy_set_header Host $host;
            proxy_set_header X-Real-IP $remote_addr;
            proxy_pass http://localhost:8913;
        }
    }

    ### install Pulsar on the hulp VM

    #### sources
    https://pulsar.readthedocs.io/en/latest/install.html
    https://phoenixnap.com/kb/how-to-install-docker-on-ubuntu-18-04
    http://pulsar.apache.org/docs/en/standalone-docker/

    install docker first

    sudo apt-get update
    sudo apt install docker.io
    sudo systemctl start docker
    sudo systemctl enable docker

    run the follwing:
    sudo $ docker run -it \
      -p 6650:6650 \
      -p 8080:8080 \
      --mount source=pulsardata,target=/pulsar/data \
      --mount source=pulsarconf,target=/pulsar/conf \
      apachepulsar/pulsar:2.5.1 \
      bin/pulsar standalone

### jobs dont upload anymore after changing data storage

#### error message
found out with the command journalctl -f -u galaxy
Jobs werent taken up anymore after editing the data storage

    error log =

    May 18 14:00:42 packer-Ubuntu-18 uwsgi[1180]: [pid: 2266|app: 0|req: 2951/2951] 82.101.220.110 () {54 vars in 1285 bytes} [Mon May 18 14:00:42 2020] GET /api/histories/d413a19dec13d11e/contents?order=hid&v=dev&q=update_time-ge&q=deleted&q=purged&qv=2020-05-18T12%3A00%3A27.000Z&qv=False&qv=False => generated 481 bytes in 27 msecs (HTTP/1.1 200) 3 headers in 139 bytes (1 switches on core 0)
    May 18 14:00:43 packer-Ubuntu-18 uwsgi[1180]: galaxy.jobs.mapper DEBUG 2020-05-18 14:00:43,048 [p:2272,w:0,m:2] [JobHandlerQueue.monitor_thread] (53) Mapped job to destination id: slurm
    May 18 14:00:43 packer-Ubuntu-18 uwsgi[1180]: galaxy.jobs.handler DEBUG 2020-05-18 14:00:43,053 [p:2272,w:0,m:2] [JobHandlerQueue.monitor_thread] (53) Dispatching to slurm runner
    May 18 14:00:43 packer-Ubuntu-18 uwsgi[1180]: galaxy.jobs DEBUG 2020-05-18 14:00:43,059 [p:2272,w:0,m:2] [JobHandlerQueue.monitor_thread] (53) Persisting job destination (destination id: slurm)
    May 18 14:00:43 packer-Ubuntu-18 uwsgi[1180]: galaxy.jobs.handler ERROR 2020-05-18 14:00:43,060 [p:2272,w:0,m:2] [JobHandlerQueue.monitor_thread] failure running job 53
    May 18 14:00:43 packer-Ubuntu-18 uwsgi[1180]: Traceback (most recent call last):
    May 18 14:00:43 packer-Ubuntu-18 uwsgi[1180]:   File "/srv/galaxy/server/lib/galaxy/jobs/handler.py", line 388, in __handle_waiting_jobs
    May 18 14:00:43 packer-Ubuntu-18 uwsgi[1180]:     self.dispatcher.put(self.job_wrappers.pop(job.id))
    May 18 14:00:43 packer-Ubuntu-18 uwsgi[1180]:   File "/srv/galaxy/server/lib/galaxy/jobs/handler.py", line 987, in put
    May 18 14:00:43 packer-Ubuntu-18 uwsgi[1180]:     self.job_runners[runner_name].put(job_wrapper)
    May 18 14:00:43 packer-Ubuntu-18 uwsgi[1180]:   File "/srv/galaxy/server/lib/galaxy/jobs/runners/__init__.py", line 152, in put
    May 18 14:00:43 packer-Ubuntu-18 uwsgi[1180]:     job_wrapper.enqueue()
    May 18 14:00:43 packer-Ubuntu-18 uwsgi[1180]:   File "/srv/galaxy/server/lib/galaxy/jobs/__init__.py", line 1431, in enqueue
    May 18 14:00:43 packer-Ubuntu-18 uwsgi[1180]:     self._set_object_store_ids(job)
    May 18 14:00:43 packer-Ubuntu-18 uwsgi[1180]:   File "/srv/galaxy/server/lib/galaxy/jobs/__init__.py", line 1453, in _set_object_store_ids
    May 18 14:00:43 packer-Ubuntu-18 uwsgi[1180]:     object_store_populator.set_object_store_id(dataset)
    May 18 14:00:43 packer-Ubuntu-18 uwsgi[1180]:   File "/srv/galaxy/server/lib/galaxy/objectstore/__init__.py", line 1013, in set_object_store_id
    May 18 14:00:43 packer-Ubuntu-18 uwsgi[1180]:     self.object_store.create(data.dataset)
    May 18 14:00:43 packer-Ubuntu-18 uwsgi[1180]:   File "/srv/galaxy/server/lib/galaxy/objectstore/__init__.py", line 845, in create
    May 18 14:00:43 packer-Ubuntu-18 uwsgi[1180]:     self.backends[0].create(obj, **kwargs)
    May 18 14:00:43 packer-Ubuntu-18 uwsgi[1180]:   File "/srv/galaxy/server/lib/galaxy/objectstore/__init__.py", line 406, in create
    May 18 14:00:43 packer-Ubuntu-18 uwsgi[1180]:     safe_makedirs(dir)
    May 18 14:00:43 packer-Ubuntu-18 uwsgi[1180]:   File "/srv/galaxy/server/lib/galaxy/util/path/__init__.py", line 114, in safe_makedirs
    May 18 14:00:43 packer-Ubuntu-18 uwsgi[1180]:     makedirs(path)
    May 18 14:00:43 packer-Ubuntu-18 uwsgi[1180]:   File "/srv/galaxy/venv/lib/python3.6/os.py", line 220, in makedirs
    May 18 14:00:43 packer-Ubuntu-18 uwsgi[1180]:     mkdir(name, mode)
    May 18 14:00:43 packer-Ubuntu-18 uwsgi[1180]: PermissionError: [Errno 13] Permission denied: '/mnt/galaxy-datadata/000'
    May 18 14:00:45 packer-Ubuntu-18 uwsgi[1180]: 95.97.100.162 - - [18/May/2020:14:00:45 +0200] "GET /api/histories/2d9035b3fc152403/contents?details=f1b9846ab84237e7&order=hid&v=dev&q=update_time-ge&q=deleted&q=purged&qv=2020-05-18T12%3A00%3A40.000Z&qv=False&qv=False HTTP/1.1" 200 - "https://fairheartgalaxy.bioinformatics-atgm.nl/workflows/list" "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/81.0.4044.138 Safari/537.36"


#### solved by
Problem detected = mounted image in /mnt/galaxy-data/000 was protected \
Solution + give the rights to edit, change, add and remove data to the whole /mnt folder \
 fixed by the following command: ``sudo chmod a+rwx /mnt``




 
