# SRE Handson Ubuntu Gogs Go

## Task 0: Create a virtual machine Ubuntu 18.04 server 64-bit version.

#### Installation Guide - Virtual Machine ( Hypervisor )
Host Operating system Mac (High Sierra v10.13.5): 

##### Command: 
    brew install virtualbox

### VirtualBox on macOS High Sierra v10.13.5. Here’s the error he kept encountering:
#### Do following steps for fix: 
- Open up System Preferences
- Click on theSecurity & Privacy icon
- Make sure Allow apps downloaded from: App store and identified developers is checked.

### Download ISO image
Ubuntu distribution: https://ubuntu.com/download/server#releases
- Setup Ubuntu 18.04 server with 2 x vCPUs and 4GB RAM
- Enable SSH to server

## Task 1: Upgrade Operating System
Configuring Port Forwarding with NAT:

   ##### Command: 
       VBoxManage modifyvm "Ubuntu 18.04 Server Fresh" --natpf1 "GuestSSH,tcp,127.0.0.1,2422,,22"
    
   - Here’s the error he kept encountering:

   To modify VM configuration usign above command, Need to stop VM or use ```controlvm``` command.
    
   ##### Command: 
       VBoxManage controlvm "Ubuntu 18.04 Server Fresh" natpf1 "GuestSSH,tcp,127.0.0.1,2422,,22"
   
   Connect Guest machine VM from Host machine:

   ##### Command: 
       ssh user@127.0.0.1 -p 2422
   
   ### Upgrade the Ubuntu system packages
   ##### Command: 
       sudo apt update
   ##### Command: 
       sudo apt upgrade
  
   ### Upgrade the latest hwe kernel
   #Ref: https://wiki.ubuntu.com/Kernel/LTSEnablementStack#Kernel.2FSupport.A18.04.x_Ubuntu_Kernel_Support
      
   ##### Command: 
      sudo apt-get install --install-recommends linux-generic-hwe-18.04
      
- what is the IP of the guest machine? ```127.0.0.1```
- Is it accessible from the host machine? ```Yes```
- What configuration is required in order to have ssh access?
##### Command: 
       VBoxManage modifyvm "Ubuntu 18.04 Server Fresh" --natpf1 "GuestSSH,tcp,127.0.0.1,2422,,22"
  
  ## Task 2: Install Docker Engine
  ### Update and install package
##### Command: 
      sudo apt-get update
#
      sudo apt-get install \
      apt-transport-https \
      ca-certificates \
      curl \
      gnupg \
      lsb-release

##### Add Docker’s official GPG key:
##### Command:
      curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /usr/share/keyrings/docker-archive-keyring.gpg
##### Use the following command to set up the stable repository
##### Command:
       echo \
       "deb [arch=amd64 signed-by=/usr/share/keyrings/docker-archive-keyring.gpg] https://download.docker.com/linux/ubuntu \
       $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
       
##### Update the apt package index, and install the latest version of Docker Engine and containerd, or go to the next step to install a specific version:
##### Command:
      sudo apt-get update
      sudo apt-get install docker-ce docker-ce-cli containerd.io
      
#### Well-Known Issue
##### Command:
      docker run hello-world
      docker: Got permission denied while trying to connect to the Docker daemon socket at unix:///var/run/docker.sock: Post                  
      http://%2Fvar%2Frun%2Fdocker.sock/v1.24/containers/create: dial unix /var/run/docker.sock: connect: permission denied.
      See 'docker run --help'.
      
##### To Fix : Add user to properly use the docker commands
##### Command: ( Logged out and logged in again to take effect the command according )
      sudo usermod -ag docker $USER
      
##### Run docker hello world to verify the installation.
##### Command:
      $ docker run hello-world

          Hello from Docker!
          This message shows that your installation appears to be working correctly.

          To generate this message, Docker took the following steps:
           1. The Docker client contacted the Docker daemon.
           2. The Docker daemon pulled the "hello-world" image from the Docker Hub.
              (amd64)
           3. The Docker daemon created a new container from that image which runs the
              executable that produces the output you are currently reading.
           4. The Docker daemon streamed that output to the Docker client, which sent it
              to your terminal.

          To try something more ambitious, you can run an Ubuntu container with:
           $ docker run -it ubuntu bash

          Share images, automate workflows, and more with a free Docker ID:
           https://hub.docker.com/

          For more examples and ideas, visit:
           https://docs.docker.com/get-started/

##### What are the relationships of the three components in Docker Engine, aka. docker-ce docker-ce-cli and containerd.io?

- containerd.io : daemon containerd. It works independently on the docker packages, and it is required by the docker packages.
  containerd is available as a daemon for Linux and Windows. It manages the complete container lifecycle of its host system, from image transfer and storage to    
  container execution and supervision to low-level storage to network attachments and beyond.

- docker-ce-cli : command line interface for docker engine, community edition

- docker-ce : docker engine, community edition. Requires docker-ce-cli.

## Task 3: Install Gogs (a self-hosted git service)
##### Command: Pull docker image gogs
      docker pull gogs/gogs
      
#### Default Gogs run on : 3000 port so change it into 3100
##### Command: Run Docker as detached mode on port: 3100 and publish 3100
      docker run --name=gogs -p 10022:22 -d -p 3100:3100 -v /var/gogs:/data gogs/gogs
      
#### How to check if it is up and running as expected?      
##### Command: 
      sudo netstat -plnt | grep 3100
        tcp        0      0 0.0.0.0:3100            0.0.0.0:*               LISTEN      25276/docker-proxy  
        tcp6       0      0 :::3100                 :::*                    LISTEN      25281/docker-proxy  
        

          
##### Command: To modify VM configuration usign above command
       VBoxManage controlvm "Ubuntu 18.04 Server Fresh" natpf1 "GogUI,tcp,127.0.0.1,3100,,3100"

#### Well-Known Issue of URL
##### User can change configuration in future directly modify app.ini file in following directory of VM
    /var/gogs/gogs/conf

   ##### For the first time, configure it following the installation steps 
   ###### Database uses SQLite3 (no external DB required)
   ###### Figure out the domain, ports and URL to be used
   ###### Create an admin user as well (username: root password: 123456)

# Task 4: Create a Demo Organization/Repository in Gogs
![alt_text](https://github.com/punit5658/interview/blob/main/task-4.png)

##### Add Configuration before add first commit
        git config --global user.email "test@test.com"
        git config --global user.name "test"
        
##### Following command help to commit file:
        touch README.md
        git init
        git add README.md
        git commit -m "first commit"
        git remote add origin http://127.0.0.1:3100/demo/go-web-hello-world.git
        git push -u origin master
        
        

        
