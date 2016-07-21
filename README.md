# geofag-IaaS

General documentation on openstack can be found [here](https://iaas.readthedocs.io/en/latest/project/index.html).

Here we describe how we set-up openstack server for the Department of
 Geosciences i.e. where most common software and libraries used in our department are installed and available.

## Introduction and setup

Before you can create and use any of the servers below, you need to set-up authorization on your local machine (the one you will be using to connect to your openstack server).

Do not repeat this set-up procedure if you had done it before (even for a different openstack session, etc.). THIS IS MEANT TO BE DONE ONCE ONLY!

1. Open a web browser at [https://access.iaas.uio.no/](https://access.iaas.uio.no/) and follow the steps described at [Firt time login](https://iaas.readthedocs.io/en/latest/enduser/login.html#first-time-login)
2. [Check on your local machine your SSH keys](https://help.github.com/articles/checking-for-existing-ssh-keys/). 
3. [SKIP THIS STEP IF YOU HAVE AN SSH KEY] If you do not have an SSH key, create a [new SSH key](https://help.github.com/articles/generating-a-new-ssh-key-and-adding-it-to-the-ssh-agent/). 
4. Import your SSH key in openstack. Follow instructions in section ["Import an existing key"](https://iaas.readthedocs.io/en/latest/enduser/create-virtual-machine.html#importing-an-existing-key) (and stop at "Letting OpenStack create a keypair").
5. Allow SSH and ICMP access. Follow instructions at [https://iaas.readthedocs.io/en/latest/enduser/create-virtual-machine.html#allowing-ssh-and-icmp-access](https://iaas.readthedocs.io/en/latest/enduser/create-virtual-machine.html#allowing-ssh-and-icmp-access)
5. Get familiar with [IaaS dashboard](https://iaas.readthedocs.io/en/latest/enduser/dashboard.html).


Now you are ready to create an openstack server. Choose in this list which use case is the most appropriate for you (and let us know if you need more configuration):

1. [openstack server for Geosciences](config/gp_geofag.md)
2. [jupyter notebook](config/jupyter.md)
3. [Medium-size simulations on openstack (WRF, FLEXPART, openIFS)](config/simulations.md)
4. [Big-data](config/bd.md)
