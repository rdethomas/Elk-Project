# Elk-Project Deployment
 - Setting up virtual machines network with monitoring

The files in this repository were used to configure the network depicted below.

The topology of this project is found in this folder:Diagrams/Elk_project.png

These files have been tested and used to generate a live ELK deployment on Microsoft Azure. These can be used to either recreate the entire deployment pictured above. Alternatively, you may use certain files in the ansible folder (path below) install only certain pieces of it, such as Filebeat. The files contain 'dummy' IPs but with directions on where you should enter you own configurations such as your own IP address.

  - Ansible/ansible.cfg
  - Ansible/filebeat-config.yml
  - Ansible/filebeat-playbook.yml
  - Ansible/metricbeat-config.yml
  - Ansible/metricbeat-playbook.yml
  - Ansible/install-elk.yml

This document contains the following details:
- Description of the Topologu
- Access Policies
- ELK Configuration
  - Beats in Use
  - Machines Being Monitored
- How to Use the Ansible Build


### Description of the Topology

The main purpose of this network is to expose a load-balanced and monitored instance of DVWA, the Damn Vulnerable Web Application.

Load balancing ensures that the application will be highly available, in addition to restricting inboud access to the network.
The load balancer ensures that work to process incoming traffic will be shared by both vulnerable web servers. Access controls will ensure that only authorized users will be able to connect in the first place. 

The advantage of a Jump Box is that is the environment where all tasks are launched. Due to this we can refer to the Jump Box as the Secure Admin Workstation. Any admin will need to connect to the Jump Box before conducting any tasks. 
Integrating an ELK server allows users to easily monitor the vulnerable VMs for changes to the file systems of the VMs on the network (filebeat), as well as watch system metrics such as attempted SSH logins (metricbeat).

The configuration details of each machine may be found below.


| Name     | Function | IP Address | Operating System |
|----------|----------|------------|------------------|
| Jump Box | Gateway  | 10.0.0.4   | Linux            |
| DVWA 1   |Web Server| 10.0.0.5   | Linux            |
| DVWA 2   |Web Server| 10.0.0.6   | Linux            |
| ELK      |Monitoring| 10.1.0.1   | Linux            |

### Access Policies

The machines on the internal network are not exposed to the public Internet. 

Only the Jump Box machine can accept connections from the Internet. Access to this machine is only allowed from the following IP addresses <Personal_IP_Address>

Machines within the network can only be accessed by each other via the SSH Public Key configured in each virtual machine. The DVWA 1 and 2 VMs send traffic to the ELK server.

A summary of the access policies in place can be found in the table below.

| Name     | Publicly Accessible | Allowed IP Addresses |
|----------|---------------------|----------------------|
| Jump Box |    Yes              | Personal IP          |
| ELK      |    No               |10.1.0.0/16&PersonalIP|
| DVWA 1   |    No               | 10.0.0.0/16          |
| DVWA 2   |    No               | 10.0.0.0/16          |

### Elk Configuration

Ansible was used to automate configuration of the ELK machine. No configuration was performed manually, which is advantageous because you can deploy quickly and efficiently. You are able to configure multiple machines with one command. 
 
The playbook implements the following tasks:

- Create a New VM (should be named something simple like "Elk-Server") Keep note of the Private IP (10.1.0.0/16) and the Public IP (0.0.0.0) you will need the Private IP to SSH into the VM and the Public IP to connect to the Kibana Portal (HTTP Site) to view all Metrics/Syslogs.
- Download and Configure the "elk-docker" container "In the hosts.conf you will need to add a new group [elkservers] and their respective Private IP to the group. Then create a new ansible-playbook that will download, install, configure the "Elk-Server" to map the following ports [5601,9200,5044], and starts the container.
- Launch and expose the container once is started. You can verify that the container is up with SSH command to the container from your JumpBox (SAW). Once in the [Elk-Server] run the command [sudo docker ps]
- Create new Inbound Security Rules to allow Ports: 5601 and 9200 "The Inbound Security Rules should allow access from your Personal IP"
- Open a new browser and type in the browser http://[your.VM.IP]:5601/app/kibana to access the Kibana Portal site.

The following screenshot displays the result of running `docker ps` after successfully configuring the ELK instance: Images/docker_ps


### Target Machines & Beats
This ELK server is configured to monitor the following machines: 10.0.0.5 and 10.0.0.6

We have installed the following Beats on these machines: Filebeat and Metricbeat 

These Beats allow us to collect the following information from each machine:

- Filebeat is a lightweight shipper for forwarding and centralizing log data. It monitors log files or locations you specify, collects log events, and forwards them either to Elasticsearch or Logstash for indexing.

- Metricbeat collects metrics from the operating system and from services running on the server. The program forwards these logs to the kibana dashboard for visualization and analysis.


### Using the Playbook
In order to use the playbook, you will need to have an Ansible control node already configured. Assuming you have such a control node provisioned: 

SSH into the control node and follow the steps below:
- Copy/download the filebeat.yml file to the /etc/ansible/roles/files/ directory.
- Update the configuration file to include the Private IP of the Elk-Server to the ElasticSearch and Kibana sections of the configuration file.
- Create a new playbook in the /etc/ansible/roles/ directory that will install, drop in the updated configuration file, enable and configure system module, run the filebeat setup, and start the filebeat service.
- Run the playbook, and navigate to ELk-Server to check that the installation worked as expected by using the docker ps once command.


- Which file is the playbook? Where do you copy it?
  The playbook is called <filebeat-playbook.yml> and it is located in Ansible/ filebeat-playbook 
- Which file do you update to make Ansible run the playbook on a specific machine? How do I specify which machine to install the ELK server on versus which to install Filebeat on?_
  The file needed is the filebeat-config.yml file which is a configuration file that will be dropped into the Elk-Server during the run of the ansible-playbook. When the host.cfg file in the ansible directory is updated you will need to create a new group called [elkservers] and add their respective Private IP of the Elk-Server. When configuring the filebeat-config.yml file you need to add the Private IP of the Elk-Server in lines 1106 and 1806.
- Which URL do you navigate to in order to check that the ELK server is running? 
  The web address is http://[your.VM.IP]:5601/app/kibana 

- Below are the commands to run the Ansible configuration for the Elk-Server:

- ssh RedAdmin@<JumpBoxIP>
- sudo docker container list -a #locate your ansible container
- sudo docker start container #starts your container
- sudo docker attach container #adds a shell to your container
- cd /etc/ansible/ #command to move to the ansible folder 
- ansible-playbook playbook-elk.yml #configures the Elk-Server and starts the container 
- cd /etc/ansible/roles/ #move to the ansible roles folder
- ansible-playbook filebeat-playbook.yml #deploys and configures filebeat in your container
- open a new web browser http://[your.VM.IP]:5601/app/kibana #accessing this link displays the Kibana dashboard

You will need to ensure all files are properly placed before running the ansible-playbooks.

