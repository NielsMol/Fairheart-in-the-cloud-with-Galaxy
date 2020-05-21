 
### Ephemeris for galaxy tool:
Who likes it to get all your tools installed by hand !?\
not me, thats why there is this handy tools who does it for you, you only have to upload a file with desired tools and versions

#### ephimerus
install ephimerus:
``virtualenv -p python3 ephemeris_venv``\
``. ephemeris_venv/bin/activate``\
``pip install ephemeris``



#### download the mapping workflow
``wget https://galaxyproject.github.io//training-material/topics/sequence-analysis/tutorials/mapping/workflows/mapping.ga``

##### note
diable external authenthication by adding # before the lines you added. the authenthication prevents the command code to get access (permission deied)
APT-key is obtained to create an user acount and then search as setting generate api key, the account must be a admin user.
place # in front of set_remote_user  to enable that after website login users must also create an useracount

create a list of items desired to install:

``workflow-to-tools -w mapping.ga -o workflow_tools.yml -l "mapping tools"``

#### installing Tools

go to the galaxy webbrowser\
login useing the admin email\
or make existign email and admin\
user -> preference and manage API key\
 select old one or create a new one

Install the tools with this command: (please edit the command ir it will not work )\
``shed-tools install -g https://your-galaxy -a <api-key> --name bwa --owner devteam --section_label Mapping``
 

``sudo cp /galaxy1/fakelerootx1.pem /usr/local/share/ca-certificates``\
``cd /usr/share/ca-certificate``\
``sudo mv fakelerootxl.pem fekeleroot.pem.crt``\
``update-ca-certificates``\
``export REQUESTS_CA_BUNDLE=/etc/ssl/certs/ca-certificates.crt``


check if tools are installed in the galaxy ui admin


you can check at other galaxy instances which tools they use, version and make a nice list to install them also.\
or check your own instance and browse for amazing tools\
#### sources
https://training.galaxyproject.org/training-material/topics/admin/tutorials/tool-management/tutorial.html
