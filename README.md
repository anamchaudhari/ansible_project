Project: Server Provisioning with Ansible

Objective : Automate the provisioning of a basic server setup using Ansible.

Requirements:
1. Virtual Machines or Servers: You can use virtual machines (e.g., VirtualBox, VMware) or cloud instances (e.g., AWS, Azure, Google Cloud) to act as your target servers.
2. Ansible Installed: Ensure Ansible is installed on your local machine. You can install Ansible using pip or your system's package manager.
3. SSH Access: Ensure that you can SSH into your target servers from your Ansible control machine without needing to enter a password each time (you can set up SSH keys for this).

Steps:

Created two EC2 instance on AWS console one as a server and another as a target

On server instance :
Install ansible 
    Sudo yum install ansible -y
    ansible —version 
Created user ansible and set its password 
To check user is created or not 
    cat /etc/passwd
Now, make changes in configurations i.e. hosts/inventory files
vi /etc/ansible/hosts
Mention group name for example I have taken ‘grp’ as group name and IP address / DNS where you want the connection 
   [grp]
   <private ip address>
vi /etc/ansible/ansible_cfg
   [defaults]
   Inventory = /etc/anisble/hosts (the path where u have saved the host ip address)
   sudo_user = root

Open sudoer file 
visudo  
make below changes
  (#Allow root to run any commands anywhere)
  root    ALL=(ALL)       ALL
  ansible ALL=(ALL) NOPASSWD: ALL (add the user which you have created here)

Once this is done login as Ansible user using 

  su - ansible
Put the pass that you have for the ansible user
Once logged in check with the executing command that you have permission to install or work as an Ansible user 
yum install update -y
(If this works you have successfully set the ansible user i.e. ansible server)

Now try connecting the target node through 
 ssh <ip address>
It will ask password give the pass which u have set during creating the user on the target node
Once you have logged in successfully try installing a https server by using sudo command
 Sudo yum install httpd -y 
If this runs successfully that mean all your configuration is done correctly 


To login into node every time it will ask for a password to avoid it create a public and private key
go to vi /etc/ssh/sshd_config (note this is not on the ansible server but on the instance itself)
Comment and Uncomment these parameters accordingly
  # restarting sshd in the default instance launch configuration.
  PasswordAuthentication yes
  #PermitEmptyPasswords no

  (#Authentication:)
  #LoginGraceTime 2m
  PermitRootLogin yes
  #StrictModes yes

Now go to the ansible server again and create a public and private key 
  Ssh-keygen
Once done, you will see a hidden .ssh folder
  cd .ssh
  Ls
  You will see public key and private key generated

  ssh-copy-id username@ip_address 
(Press enter on each question asked)
Put the password one last time and then you will be in the target instance 


Once we made sure we could connect and do the necessary things on the target instance 
let's create a playbook and provision an HTTPD server (if there is an HTTPD server on your target remove it)

Dry run to check if your playbook is correct
  Ansible-playbook test.yml —check
This will give information on whether the playbook will execute successfully or not

Now you can run the playbook

Ansible-playbook test.yml

You will see output something like this 
172.31.14.144              : ok=3    changed=2    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0  

Now you can check on your target instance whether httpd is installed and started or not

Which httpd
Service httpd status
