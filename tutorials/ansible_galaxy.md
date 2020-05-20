### Install Ansible



`` sudo apt update``\
`` sudo apt install software-properties-common``\
`` sudo apt-add-repository --yes --update ppa:ansible/ansible``\
`` sudo apt install ansible``\
`` make deb``\
`` mkdir intro || cd intro``

`` nano my_hosts``

add the folowing to ``my_hosts``:


	[my_hosts]
	localhost ansible_connection=local

preform the following commands to create the proper location of ``main.yml``\
`` mkdir -p roles/myrole/tasks/``\
`` cd roles/myrole/tasks/``\
`` nano main.yml``

add the following to ``main.yml``:


	---
	- name: Copy a file to the remote host
	copy:
		src: test.txt
		dest: /tmp/test.txt


`` mkdir roles/my-role/files``\
`` cd roles/my-role/files``\
`` nano test.txt``


add the following to ``test.txt``:


	"hello Galaxy"

edit the playbook:
	
`` nano playbook.yml``

put the following in ``playbook.yml``:




	---
	- hosts: my_hosts
		roles:
			- my-role
			


Run playbook

`` ansible-playbook -i my-host playbook.yml``

setting up the module:

``ansible -i my_host -m setup my_hosts | less``\
	crtl-Z to go out

 Create the templates:

`` mkdir roles/my-role/templates``
 
`` mkdir roles/my-roles/defaults``

`` cd roles/my-roles/defaults/``

`` nano main.yml``

 Add the following to ``main.yml``:


	---
	server_name: Cats!

Edit the test.ini.js file:

`` cd roles/my-role/templates``

`` nano test.ini.j2``

add the following to ``test.ini.j2`` :


	[example]
	server_name = {{ server_name }}
	listen = {{ ansible_default_ipv4.address }}


`` cd roles/my-role/tasks``\
`` nano main.yml``

add the following  in ``main.yml``:

 	- name: Template the configuration file
 	 template:
   	 src: test.ini.j2
   	 dest: /tmp/test.ini

	
	
`` ansible-playbook -i my-hosts playbook.yml``\
*note, activate command in intro directionary

edit the playbook and my hosts\
`` mkdir group_vars in root``\
`` nano group_vars/my_hosts.yml``

 add the following in ``my_host.yml``:\
	---
	server_name: Dogs!



`` ansible-playbook -i my-host playbook.yml --check --diff``

### get ansible galaxy
here is where the magic happens of the galaxy

`` ansible-galaxy install -p roles/ geerlingguy.git``\
`` nano playbook.yml in intro``

 add this at the bottem of  ``my-roles/defaults``:\
		``geerlingguy.git``
add this under  hosts in the file ``my_hosts``:\
		``become : true``
	
Run the playbook again	
 ``ansible-playbook playbook.yml``
 
 
 ## installing Galaxy with ansible
 
 On VM in Surfsara, install that it will be 2 VCPU
 do this on the UI of Surfsara
 
 Install python 3\
 `` sudo apt isntall python3``
 
make sure the following is okay:
- ansible version is >=2.7
- VM has a publis DSN name, we will also set ip an SSL certificates
- python3 is installed
- you have an inventory file with the VM or host specifiec where you will deploy galaxy


`` mkdir galaxy``\
`` cd galaxy``


add the folowing to requirements.yml\
``nano requirements.yml``


	- src: galaxyproject.galaxy
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
	  version: 0.1.3\
  

 install the roles\
 `` ansible-galaxy install -p roles -r requirements.yml``\
 
 add the following to ansible.cfg\
 `` nano ansible.cfg``
 
 
	 [defaults]\
	interpreter_python = /usr/bin/python3\
	inventory = hosts\
	retry_files_enabled = false\
	 [ssh_connection]\
	pipelining = true\ 




edit my-hosts\
`` cp ~/intro/my-hosts ./``\
`` nano my-hosts``
add the folowing to my-hosts:

 	[galaxyservers]
	training-0.example.org ansible_connection=local


*note: training-0.example.org to your web-url
The server layout should look like this:\
├── ansible.cfg\
├── galaxy.yml\
├── group_vars\
│   └── galaxyservers.yml\
├── hosts\
├── requirements.yml\
└── roles\
    ├── galaxyproject.galaxy\
    │   ├── defaults\
    │   │   └── main.yml\


### Sources

@misc{admin-ansible,
    author = "Helena Rasche and Saskia Hiltemann",
    title = "Ansible (Galaxy Training Materials)",
    year = "2020",
    month = "05",
    day = "06"
    url = "\url{/training-material/topics/admin/tutorials/ansible/tutorial.html}",
    note = "[Online; accessed Wed May 20 2020]"
}
@article{Batut_2018,
        doi = {10.1016/j.cels.2018.05.012},
        url = {https://doi.org/10.1016%2Fj.cels.2018.05.012},
        year = 2018,
        month = {jun},
        publisher = {Elsevier {BV}},
        volume = {6},
        number = {6},
        pages = {752--758.e1},
        author = {B{\'{e}}r{\'{e}}nice Batut and Saskia Hiltemann and Andrea Bagnacani and Dannon Baker and Vivek Bhardwaj and Clemens Blank and Anthony Bretaudeau and Loraine Brillet-Gu{\'{e}}guen and Martin {\v{C}}ech and John Chilton and Dave Clements and Olivia Doppelt-Azeroual and Anika Erxleben and Mallory Ann Freeberg and Simon Gladman and Youri Hoogstrate and Hans-Rudolf Hotz and Torsten Houwaart and Pratik Jagtap and Delphine Larivi{\`{e}}re and Gildas Le Corguill{\'{e}} and Thomas Manke and Fabien Mareuil and Fidel Ram{\'{\i}}rez and Devon Ryan and Florian Christoph Sigloch and Nicola Soranzo and Joachim Wolff and Pavankumar Videm and Markus Wolfien and Aisanjiang Wubuli and Dilmurat Yusuf and James Taylor and Rolf Backofen and Anton Nekrutenko and Björn Grüning},
        title = {Community-Driven Data Analysis Training for Biology},
        journal = {Cell Systems}
}

https://training.galaxyproject.org/training-material/topics/admin/tutorials/ansible/tutorial.html#your-first-playbook-and-first-role               
https://docs.ansible.com/ansible/latest/installation_guide/intro_installation.html
