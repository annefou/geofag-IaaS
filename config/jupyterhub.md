# Set-up a jupyter notebook 

Our goal is to set-up an openstack server and use it for running a Jupyter notebook web application. This can be used for teaching as it ensures all your students have the same python packages and environment.



Follow the steps for install jupyter notebook on openstack. Here we assume you have done it and give you information on how to build a jupyterhub server from a jupyter notebook openstack installation

### 1. Login to your jupyter notebook instance 

**From a Linux** (and probably Mac, even though I did not test it) machine:

To access your new created instance (and you are the only one to be able to login thanks to the usage of your SSH keypair), you do not use your UIO username but the generic ubuntu username

ssh -Y -i $HOME/.ssh/id_rsa ubuntu@158.37.63.220

**From a Windows machine**:

TO BE DONE
 
### 2. Installation de jupyterhub (for running python 3 notebooks):

See https://zonca.github.io/2016/04/jupyterhub-sdsc-cloud.html

sudo  apt-get update


- Add hostname in your hosts file:

sudo vi /etc/hosts

My instance is called jupyterhub so I added geofag on the first line:

127.0.0.1 localhost jupyterhub

(I had 127.0.0.1 localhost)

sudo apt-get install npm

sudo apt-get install nodejs-legacy

sudo npm install -g configurable-http-proxy

conda install traitlets tornado jinja2 sqlalchemy 

pip install jupyterhub

sudo adduser ubuntu shadow

sudo apt install nginx

sudo mkdir /etc/nginx/ssl

sudo openssl req -x509 -nodes -days 365 -newkey rsa:2048 -keyout /etc/nginx/ssl/nginx.key -out /etc/nginx/ssl/nginx.crt

Get /etc/nginx/nginx.conf from https://gist.github.com/zonca/08c413a37401bdc9d2a7f65a7af44462 and put it in /etc/nginx/nginx.conf

sudo cp /etc/nginx/nginx.conf /etc/nginx/nginx.conf.ori

sudo cp nginx.conf /etc/nginx/nginx.conf

sudo apt update

sudo apt install apt-transport-https ca-certificates

sudo apt-key adv --keyserver hkp://p80.pool.sks-keyservers.net:80 --recv-keys 58118E89F3A912897C070ADBF76221572C52609D 

echo "deb https://apt.dockerproject.org/repo ubuntu-wily main" | sudo tee /etc/apt/sources.list.d/docker.list

sudo apt-get update

sudo apt-get purge lxc-docker

apt-cache policy docker-engine

sudo apt-get install linux-image-extra-$(uname -r)

sudo apt install docker-engine

sudo usermod -aG docker ubuntu

- Logout and login again for the group to take effect


pip install dockerspawner

docker pull jupyter/systemuser <-- it fails if you forgot to logout and login again...

conda install ipython jupyter


- Create jupyterhub_config.py in the home folder of the ubuntu user with this content:

c.JupyterHub.confirm_no_ssl = True
c.JupyterHub.spawner_class = 'dockerspawner.SystemUserSpawner'

\# The docker instances need access to the Hub, so the default loopback port doesn't work:
from IPython.utils.localinterfaces import public_ips
c.JupyterHub.hub_ip = public_ips()[0]

### 3. Start jupyterhub:

From the home folder of the ubuntu user, type

jupyterhub

 to launch the Jupyterhub process, see below how to start it automatically at boot. Use CTRL-C to stop it.


###Pending problem: I still can't access jupyterhub from outside... I need help to fix it.