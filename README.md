# Network Automation Framework Examples

This is a collection of demonstrations used for the ["Creating a Network Automation Framework"](https://www.redhat.com/en/events/creating-network-automation-framework) webinar presented by Red Hat, Inc. These focus on automating OpenStack network resources using Ansible.

These examples assume that Ansible is installed along with the necessary "shade" Python library. It is strongly recommended to install "shade" into a virtual (isolated) Python environment. The "ansible_python_interpreter" variable will need to be modified in the Playbooks to reflect the Shade installation directory. An OpenStack lab is also required to test against.

For the Playbooks to work, the OpenStack user credentials must first be sourced into the environment. Ansible will automatically read in those values and provide it to the Ansible modules managing and authenticating against the OpenStack resources.

Example:

```
$ source ~/keystonerc_user
```

Helpful guides:

* [Installing Ansible](http://docs.ansible.com/ansible/latest/intro_installation.html)
* [Using Virtualenv to create isolated Python installations](https://virtualenv.pypa.io/en/stable/)
* Deploying an OpenStack lab using...
    * [PackStack](https://github.com/ekultails/rootpages/blob/master/markdown/openstack.md#automation---packstack---install)
        * A simple all-in-one OpenStack installation. Recommended for installing onto a virtual machine with minimal resources.
    * or [TripleO-Quickstart](https://github.com/ekultails/rootpages/blob/master/markdown/openstack.md#automation---tripleo---quick)
        * A fully virtualized deployment of TripleO (the upstream version of the Red Hat OpenStack Platform). Recommended for a dedicated and powerful server installation.


## Demo 1 - Networks

Create and destroy a simple private network stack. The network "network_demo" is created with the subnet "subnet_demo." The subnet uses the CIDR 10.0.0.0/24 and the Google resolvers 8.8.4.4 and 8.8.8.8.

Files:

* demo_1_networks/network_create.yml
* demo_1_networks/network_delete.yml

Create the network:

```
$ ansible-playbook demo_1_networks/network_create.yml
```

Delete the network:

```
$ ansible-playbook demo_1_networks/network_delete.yml
```


## Demo 2 - API

Create a simple load balancer using a combination of Ansible modules, OpenStack commands, and Neutron's API. This assumes that the "subnet_demo" was created by the first demo.

Files:

* demo_2_api/load_balancer_create.yml = A Playbook to create a load balancer.

Assumption:

* Load-balancing-as-a-service v2 is installed and configured in the lab environment.

This demonstration was created as a proof-of-concept of being able to contact the OpenStack APIs using [Ansible's uri module](http://docs.ansible.com/ansible/latest/uri_module.html). It is incomplete and does not deploy a fully usable load balancer. For more information on completing this, see:

* https://docs.openstack.org/newton/networking-guide/config-lbaas.html#lbaas-v2-operations
* https://developer.openstack.org/api-ref/load-balancer/v2/

Create the load balancer (v2):

```
$ ansible-playbook demo_2_api/load_balancer_create.yml
```


## Demo 3 - Heat

Create and destroy an all-in-one instance with a full networking stack.

Files:

* demo_3_heat/heat_create.yml = A Playbook to deploy the "all-in-one" stack from the "heat_stack_all-in-one.yml" Heat template.
* demo_3_heat/heat_delete.yml = A Playbook to delete the "all-in-one" stack.
* demo_3_heat/heat_stack_all-in-one.yml = The Heat template.

Assumptions:

* There is already a security group created called "open" that allows at least ingress and egress SSH access to the instance.
* The floating IP network created by a user with the admin role is named "external_network".
* There is an image in Glance called CirrOS. View all of the [official OpenStack images here](https://docs.openstack.org/image-guide/obtain-images.html). Most of these, including CirrOS, are in the QCOW2 format.
* The Heat template version is set to "2016-10-14" (the Newton release) and should work with newer releases of OpenStack.


Create the Heat stack:

```
$ ansible-playbook demo_3_heat/heat_create.yml
```

Delete the Heat stack:

```
$ ansible-playbook demo_3_heat/heat_delete.yml
```


## License

Apache 2.0