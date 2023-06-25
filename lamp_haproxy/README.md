LAMP Stack + HAProxy: Example Playbooks
-----------------------------------------------------------------------------

- Requires Ansible 1.2
- Expects CentOS/RHEL 6 hosts

This example is an extension of the simple LAMP deployment. Here we'll install
and configure a web server with an HAProxy load balancer in front, and deploy
an application to the web servers. This set of playbooks also have the
capability to dynamically add and remove web server nodes from the deployment.
It also includes examples to do a rolling update of a stack without affecting
the service.

(To use this demonstration with Amazon Web Services, please use the `aws` sub-directory.)

You can also optionally configure a Nagios monitoring node.

### Initial Site Setup

First we configure the entire stack by listing our hosts in the 'hosts'
inventory file, grouped by their purpose:

		[webservers]
		webserver1
		webserver2
		
		[dbservers]
		dbserver
		
		[lbservers]
		lbserver
		
		[monitoring]
		nagios

After which we execute the following command to deploy the site:

		ansible-playbook -i hosts site.yml

The deployment can be verified by accessing the IP address of your load
balancer host in a web browser: http://<ip-of-lb>:8888. Reloading the page
should have you hit different webservers.

The Nagios web interface can be reached at http://<ip-of-nagios>/nagios/

The default username and password are `nagiosadmin` / `nagiosadmin`.

### Removing and Adding a Node

Removal and addition of nodes to the cluster is as simple as editing the
hosts inventory and re-running:

        ansible-playbook -i hosts site.yml

### Rolling Update

Rolling updates are the preferred way to update the web server software or
deployed application, since the load balancer can be dynamically configured
to take the hosts to be updated out of the pool. This will keep the service
running on other servers so that the users are not interrupted.

In this example the hosts are updated in serial fashion, which means that
only one server will be updated at one time. If you have a lot of web server
hosts, this behaviour can be changed by setting the `serial` keyword in
`webservers.yml` file.

Once the code has been updated in the source repository for your application
which can be defined in the group_vars/all file, execute the following
command:

	 ansible-playbook -i hosts rolling_update.yml

You can optionally pass: `-e webapp_version=xxx` to the `rolling_update`
playbook to specify a specific version of the example webapp to deploy.


### Addition Learning Process 
What is Group_vars in Ansible?
Group_vars is an Ansible-specific folder as part of the repository structure. This folder contains YAML files created to have data models, and these data models apply to all the devices listed in the hosts. ini file. We can apply group variables to logical functions and hardware platform

What is Ansible handlers in what situations would you use them?
In Ansible, handlers are typically used to start, reload, restart, and stop services. If your playbook involves changing configuration files, there is a high chance that you'll need to restart a service so that the changes take effect.

Check About template !!!
