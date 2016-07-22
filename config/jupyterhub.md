# Set-up a jupyter notebook 

Our goal is to set-up an openstack server and use it for running a Jupyter notebook web application. This can be used for teaching as it ensures all your students have the same python packages and environment.



Follow the steps for install jupyter notebook on openstack. Here we assume you have done it and give you information on how to build a jupyterhub server from a jupyter notebook openstack installation

### 1. Login to your jupyter notebook instance 

**From a Linux** (and probably Mac, even though I did not test it) machine:

To access your new created instance (and you are the only one to be able to login thanks to the usage of your SSH keypair), you do not use your UIO username but the generic ubuntu username

ssh -Y -i $HOME/.ssh/id_rsa ubuntu@158.37.63.220

**From a Windows machine**:

TO BE DONE
 

