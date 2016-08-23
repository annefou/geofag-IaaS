# Set-up a jupyter notebook 

Our goal is to set-up an openstack server and use it for running a Jupyter notebook web application. This can be used for teaching as it ensures all your students have the same python packages and environment.



### 1. [Login](https://iaas.readthedocs.io/en/latest/enduser/login.html#subsequent-logins) to the UIO dashboard [https://dashboard.iaas.uio.no/](https://dashboard.iaas.uio.no/)



### 2. Create a [Virtual machine](https://iaas.readthedocs.io/en/latest/enduser/create-virtual-machine.html#create-a-virtual-machine) using the following resources: 


![alt text](../images/jupyter_geofag.png "Jupyter notebook")

Don't forget to tick "SSH and ICMP" in the Access & Security tab and to choose your SSH keypair (especially if you imported more than one!):
![alt text](../images/ssh_ICMP.png "Access & Security") 


### 3. Login to your jupyterhub notebook instance 

**From a Linux** (and probably Mac, even though I did not test it) machine:

To access your new created instance (and you are the only one to be able to login thanks to the usage of your SSH keypair), you do not use your UIO username but the generic ubuntu username

ssh -Y -i $HOME/.ssh/id_rsa ubuntu@158.37.63.70

**From a Windows machine**:

TO BE DONE
 
### 4. Installation de jupyterhub (for running python 3 notebooks):

See [https://zonca.github.io/2016/04/jupyterhub-sdsc-cloud.html](https://zonca.github.io/2016/04/jupyterhub-sdsc-cloud.html)

Here we used a simplified installation of jupyterhub.

## Installation of default packages ##

    sudo  apt-get update
    sudo apt-get -y install x11-apps
    sudo apt-get -y install firefox
    sudo apt-get -y install openssl
    sudo  apt-get update


## Add hostname in your hosts file ##

sudo vi /etc/hosts

My instance is called jupyterhub so I added jupyterhub on the first line:

127.0.0.1 localhost jupyterhub

(I had 127.0.0.1 localhost)

## Create a new volume ###

This step needs to be done once only i.e. you create a new volume to host your jupyterhub once only and then you just [attach](https://iaas.readthedocs.io/en/latest/enduser/manage-volumes.html#attach-a-volume-to-a-virtual-machine)/[detach](https://iaas.readthedocs.io/en/latest/enduser/manage-volumes.html#detach-a-volume-from-a-virtual-machine) it from your running instances.

To create a new volume to host software (python anaconda, jupyterhub) as well as local data (please make backup of your data and use this volume as a local storage (for processing your data), follow instructions given at [https://iaas.readthedocs.io/en/latest/enduser/manage-volumes.html](https://iaas.readthedocs.io/en/latest/enduser/manage-volumes.html) to [create a new volume](https://iaas.readthedocs.io/en/latest/enduser/manage-volumes.html#create-a-volume) and [attach it to your instance](https://iaas.readthedocs.io/en/latest/enduser/manage-volumes.html#attach-a-volume-to-a-virtual-machine).

Once created (**to be done with a new volume only**):

### Find out which device to mount: ###

    lsblk

This command will return something like:

    NAME   MAJ:MIN RM  SIZE RO TYPE MOUNTPOINT
    vda 253:0   0  20G  0 disk
    |__vda1 253:1   0  20G  0 part /
    vdb 253:16  0  20G  0 disk

The last line is the most important. Here it tells you that vdb is your new volume. (if you have vdc or any other "name", please make sure you adapt the next commands!).

### Create an ext4 File System: ###


    mkfs.ext4 /dev/vdb

### Create a new directory to mount your new volume: ###

    mkdir -p /opt/uio
    

### Mount your new volume: ###

     mount /dev/vdb /opt/uio

### Check your new volume: ###

    df -h /opt/uio

It should return something like:
    
    FilesystemSize  Used Available Use% Mounted on
    /dev/vdb  19.8G150.5M  19.2G   2% /opt/uio
 

## Installation of python 3 (anaconda) ##

    wget http://repo.continuum.io/archive/Anaconda3-4.1.1-Linux-x86_64.sh
    
    bash Anaconda3-4.1.1-Linux-x86_64.sh
    
Install it in /opt/uio/anaconda3 and agree to add python in your path in .bashrc

Now make sure you logout and login again (or source .bashrc)

### Installation of python packages for Geosciences

    conda update conda
    
    conda install --channel https://conda.anaconda.org/anaconda-nb-extensions nbbrowserpdf
    
    conda install netcdf4
    
    conda install -c anaconda basemap
    
    conda install --channel https://conda.anaconda.org/SciTools iris
    

## Installation de nodejs/npm 
    
    sudo  apt-get update
    sudo apt-get -y install npm nodejs-legacy
    

## Create an SSL Certificate on Nginx for Ubuntu

### Install nginx

    sudo apt-get update
    sudo apt-get -y install nginx

### Create certificate

    sudo mkdir /etc/nginx/ssl
    sudo openssl req -x509 -nodes -days 365 -newkey rsa:2048 -keyout /etc/nginx/ssl/nginx.key -out /etc/nginx/ssl/nginx.crt

Both files are then created in /etc/nginx/ssl


## Configure Nginx to Use SSL:

Modify your nginx configuration /etc/nginx/sites-enabled/default (used to be in nginx.conf)

Uncomment listen 443 ssl default_server;


        # SSL configuration
        #
        listen 443 ssl default_server;
        # listen [::]:443 ssl default_server;
        #
        # Note: You should disable gzip for SSL traffic.
        # See: https://bugs.debian.org/773332
        #
        # Read up on ssl_ciphers to ensure a secure configuration.
        # See: https://bugs.debian.org/765782
        #
        # Self signed certs generated by the ssl-cert package
        # Don't use them in a production server!
        #
        # include snippets/snakeoil.conf;

        root /var/www/html;

        # Add index.php to the list if you are using PHP
        index index.html index.htm index.nginx-debian.html;

        server_name _;
        #AF Add ssl certificate
        ssl_certificate /etc/nginx/ssl/nginx.crt;
        ssl_certificate_key /etc/nginx/ssl/nginx.key;
        #End AF


Restart nginx:

    sudo service nginx restart
    
Now both http://158.37.63.70/ and https://158.37.63.70/ work. You also need to accept untrusted connection for https://158.37.63.70/ because your certificate is not verified.


## Installation de jupyterhub:
    
### Install jupyterhub and sudospawner
    
    sudo npm install -g configurable-http-proxy
    
    pip install jupyterhub
    pip install --upgrade notebook
    
    pip install git+https://github.com/jupyter/sudospawner
    
### Create a new user

    sudo useradd jyroot
    
    sudo groupadd jupyterhub

    sudo visudo

Added at the end of the file:

    #AF give the new user sudo permissions only for launching the sudospawner script
    # the command(s) the Hub can run on behalf of the above users without needing a password
    # the exact path may differ, depending on how sudospawner was installed
    Cmnd_Alias JUPYTER_CMD = /opt/uio/anaconda3/bin/sudospawner
    # actually give the Hub user permission to run the above command on behalf
    # of the above users without prompting for a password
    jyroot ALL=(%jupyterhub) NOPASSWD:JUPYTER_CMD
    #End AF
    
And updated secure_path:

    Defaults	secure_path="/opt/uio/anaconda3/bin:/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin"
    
Finally, make sure you add both ubuntu and jyroot in jupyterhub group


    sudo usermod -a -G jupyterhub ubuntu
    sudo usermod -a -G jupyterhub jyroot
    
Test:

    sudo -u jyroot sudo -n -u $USER /opt/uio/anaconda3/bin/sudospawner --help
    
Should return the help of sudospawner (if you do not set the entire path it fails.


    sudo -u rhea sudo -n -u $USER echo 'fail'
returns
sudo: a password is required

Because the only available command is sudospawner

Enabling PAM for non-root:
===========================
I have:

    $ ls -l /etc/shadow
    -rw-r-----  1 root shadow   2197 Jul 21 13:41 shadow
    
Meaning the group shadow exists!

I just need to add my new user to it:
    
    sudo usermod -a -G shadow jyroot
    

(if not I need to create a new shadow group):

    sudo groupadd shadow
    sudo chgrp shadow /etc/shadow
    sudo chmod g+r /etc/shadow
    

Test PAM works:

    sudo -u jyroot python -c "import pamela, getpass; print(pamela.authenticate('$USER', getpass.getpass()))"

it should ask for password

    sudo chown jyroot /opt/uio/jupyterhub
    sudo chgrp jyroot /opt/uio/jupyterhub
    
(see https://github.com/jupyterhub/jupyterhub/wiki/Using-sudo-to-run-JupyterHub-without-root-privileges for more details)

## Creation of jupyterhub configuration file:

    
    sudo -u jyroot jupyterhub --generate-config

## Create certificate:

    cd /opt/uio/jupyterhub
    sudo -u jyroot mkdir ssl
    sudo -u jyroot openssl req -x509 -nodes -days 365 -newkey rsa:2048 -keyout /opt/uio/jupyterhub/ssl/jupyterhub.key -out /opt/uio/jupyterhub/ssl/jupyterhub.crt
    
Add it in the new configuration file:

    sudo -u jyroot vi jupyterhub_config.py
    

Test:
    sudo -u jyroot jupyterhub --JupyterHub.spawner_class=sudospawner.SudoSpawner
    
In your browser:

    https://158.37.63.70:8000/hub/login
    
(add exception again to trust this certificate)


## Create a list of authorized users:
    
    cd /opt/uio/jupyterhub
    
    sudo -u jyroot vi userlist

(I added my userid only i.e. annefou)

    # Create a new user (with a new password):
    
    PASSWORD=uniquepasswordforeachuser
    JUPYUSER=annefou
    echo $JUPYUSER:$PASSWORD::::/home/$JUPYUSER:/bin/bash | sudo newusers
    
	# Add this new user to jupyterhub group

    sudo usermod -a -G jupyterhub $JUPYUSER
    

## Start jupyterhub:
    
    cd /opt/uio/jupyterhub
    sudo -u jyroot jupyterhub
    