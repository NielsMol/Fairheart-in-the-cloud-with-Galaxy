# SSH Keys
#### TLDR

in this document is explained on how to generate a SSH key from your computer.\
This is required to acces, create a VM and allow acces of your coworkes to the VM

Check if there is a SSH key:\
`` ls -l ~/.ssh/``

make an public and private SSH-key\
``Ssh-key``
write down where to safe~\

### IMPORTANT
never share keyphrase. Also the thing will be saved into the pc so you will probably never need it again\
you can share your public key (ssh-key.pub) is the public one, the ssh-key is your private one and need to remain private
~- write long phrase (35 characters)~

using SSH agent
``ssh-agent remember ssh-key``\
``eval 'ssh-agent -s'``\
``shh-add ~/#naamfile `` where SSH-key will be safed or  ``$ ssh-add ~/.ssh/id_rsa``
	
check if the SSH key exist
``ssh-keygen ``
 - press enter like your life depends on it
 - **NO PASSWORD!**  or you would get an error in surfsara
	
``ssh-add -l``
	
``cat ~/plaats``\
``nano /home/gebruiker/.ssh/authorized_keys``

add the SSH key from coworkers to the VM in the following document to enable them to login:
``nano /home/ubuntu/.ssh/authorized_keys``


#### Source
https://doc.hpccloud.surfsara.nl/SSHkey  
https://doc.hpccloud.surfsara.nl/general-start
