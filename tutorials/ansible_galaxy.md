### Install Ansible



`` sudo apt update``\
`` sudo apt install software-properties-common``\
`` sudo apt-add-repository --yes --update ppa:ansible/ansible``\
`` sudo apt install ansible``\
`` make deb``\
`` mkdir intro || cd intro``

``$ nano my_hosts``

###### add the folowing to my_hosts


	[my_hosts]
	localhost ansible_connection=local

###### preform the following commands to create the proper location of main.yml
`` mkdir -p roles/myrole/tasks/``\
`` cd roles/myrole/tasks/``\
`` nano main.yml``

###### add the following to main.yml:


	---
	- name: Copy a file to the remote host
	copy:
		src: test.txt
		dest: /tmp/test.txt


`` mkdir roles/my-role/files``\
`` cd roles/my-role/files``\
`` nano test.txt``\
#####  add the following to test.txt


	"hello Galaxy"

##### edit the playbook
	
`` nano playbook.yml``
###### put the following in playbook.yml




	---
	- hosts: my_hosts
		roles:
			- my-role
			


Run playbook

`` ansible-playbook -i my-host playbook.yml``

##### setting up the module

``$ansible -i my_host -m setup my_hosts | less``\
	crtl-Z to go out

##### templates

``$ mkdir roles/my-role/templates``
 
``$ mkdir roles/my-roles/defaults``

``$ cd roles/my-roles/defaults/``

``$ nano main.yml``

##### Add the following to main.yml


	---
	server_name: Cats!



``$ cd roles/my-role/templates``

``$ nano test.ini.j2``

###### add the following


	[example]
	server_name = {{ server_name }}
	listen = {{ ansible_default_ipv4.address }}


`` cd roles/my-role/tasks``\
`` nano main.yml``

###### add the following  in main.yml

 - name: Template the configuration file\
  template:\
    src: test.ini.j2\
    dest: /tmp/test.ini\

	
	
`` ansible-playbook -i my-hosts playbook.yml``\
note, activate command in intro directionary

`` mkdir group_vars in root``
`` nano group_vars/my_hosts.yml``

###### add the following in my_host.yml\
	---\
	server_name: Dogs!\



`` ansible-playbook -i my-host playbook.yml --check --diff``

### get ansible galaxy
here is where the magic happens of the galaxy

`` ansible-galaxy install -p roles/ geerlingguy.git``\
`` nano playbook.yml in intro``

###### add this at the bottem of  my-roles/defaults
	geerlingguy.git
###### add this under  hosts: my_hosts
	become : true
	
Run the playbook again	
 ``ansible-playbook playbook.yml``
 
 
 ## installing Galaxy with ansible
 
 On VM in Surfsara, install that it will be 2 VCPU
 do this on the UI of Surfsara
 
 Install python 3
 `` sudo apt isntall python3``
 
make sure the following is okay
- ansible version is >=2.7
- VM has a publis DSN name, we will also set ip an SSL certificates
- python3 is installed
- you have an inventory file with the VM or host specifiec where you will deploy galaxy


`` mkdir galaxy``\
`` cd galaxy``


add the folowing to requirements.yml
``nano requirements.yml``


"- src: galaxyproject.galaxy
  version: 0.9.5
- src: galaxyproject.nginx
  version: 0.6.4
- src: galaxyproject.postgresql
  version: 1.0.2
- src: natefoo.postgresql_objects
  version: 1.1
- src:  geerlingguy.pip
  version: 1.3.0
- src: uchida.miniconda
  version: 0.3.0
- src: usegalaxy_eu.galaxy_systemd
  version: 0.1.2
- src: usegalaxy_eu.certbot
  version: 0.1.3\"
  

 install the roles
 `` ansible-galaxy install -p roles -r requirements.yml``\
 
 add the following to ansible.cfg
 `` nano ansible.cfg``
 
 
 " [defaults]\
interpreter_python = /usr/bin/python3\
inventory = hosts\
retry_files_enabled = false\
 [ssh_connection]\
pipelining = true\ "




edit my-hosts
`` cp ~/intro/my-hosts ./``\
`` nano my-hosts``


" [galaxyservers]\
training-0.example.org ansible_connection=local\"

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


### Sources
https://training.galaxyproject.org/training-material/topics/admin/tutorials/ansible/tutorial.html#your-first-playbook-and-first-role
https://docs.ansible.com/ansible/latest/installation_guide/intro_installation.html
