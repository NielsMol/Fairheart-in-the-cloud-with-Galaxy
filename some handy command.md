# handy commands
 
``systemctl status munge slurmd slurmctld`` \
checkt of all deamons are running (jobs)


``sifno``
showed nodes avaible



By upscaling refer to this or writing own tools:
# https://training.galaxyproject.org/training-material/topics/admin/tutorials/connect-to-compute-cluster/tutorial.html


## install tools

to install workflows

virtualenv -p python3 ephemeris_venv
. ephemeris_venv/bin/activate
pip install ephemeris


#### add to thiw file the tools you want to install in the right format
galaxy1/workflow_tools.yml


#### disable  exsternal authentication
by adding a # in front on set_remote_user in group_vars/galaxyservers   to prevent authenthication denied
also request API key

$ workflow-to-tools -w mapping.ga -o workflow_tools.yml -l "mapping tools"

#### sources
https://training.galaxyproject.org/training-material/topics/admin/tutorials/tool-management/tutorial.html


