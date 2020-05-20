## Installing the Database PostgreSQL

``nano group_vars/galaxyservers.yml``

add the following to ``galaxyservers.yml`` :


  	# python 3 support
     pip_virtualenv_command: /usr/bin/python3 -m virtualenv # usegalaxy_eu.certbot, usegalaxy_eu.tiaas2, galaxyproject.galaxy
     certbot_virtualenv_package_name: python3-virtualenv    # usegalaxy_eu.certbot
     >pip_package: python3-pip                               # geerlingguy.pip

    	# postgresql
      postgresql_objects_users:
      - name: galaxy
        postgresql_objects_databases:
      - name: galaxy
        owner: galaxy

edit ``galaxy.yml`` and add the folowing:\
 ``nano galaxy.yml ``
 
      ---
      - hosts: galaxyservers
        become: true
      pre_tasks:
        - name: install dependencies
          package:
          name: 'python3-psycopg2'
      roles:
        - galaxyproject.postgresql
        -role: natefoo.postgresql_objects
          become: true
          become_user: postgres\

play the playbook ``galaxy.yml``\
`` mv roles group_vars``\
`` mv ansible.cfg group_vars``\
`` ansible-playbook galaxy.yml``

check of postgres:\
`` /etc/postgresql``\
`` sudo -iu postgress``\
`` psql galaxy`` (will be empty becouse it is not installed yet

\du \l


### sources

https://training.galaxyproject.org/training-material/topics/admin/tutorials/ansible-galaxy/tutorial.html#postgresql

Helena Rasche, Nate Coraor, Simon Gladman, Saskia Hiltemann, Nicola Soranzo, 2020 Galaxy Installation with Ansible (Galaxy Training Materials). /training-material/topics/admin/tutorials/ansible-galaxy/tutorial.html Online; accessed Wed May 20 2020 
Batut et al., 2018 Community-Driven Data Analysis Training for Biology Cell Systems 10.1016/j.cels.2018.05.012
