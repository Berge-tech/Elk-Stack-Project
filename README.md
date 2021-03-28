Automated ELK Stack Deployment

The files in this repository were used to configure the network depicted below.

![image](https://user-images.githubusercontent.com/73409624/112742436-1f4b8580-8f54-11eb-87ba-25155f4e04ae.png)

These files have been tested and used to generate a live ELK deployment on Azure. They can be used to either recreate the entire deployment pictured above. Alternatively, select portions of the playbook (.yml) file may be used to install only certain pieces of it, such as Filebeat.

The following Ansible-playbooks are needed to create and install DVWA, the ELK-server, Filebeat and Metricbeat.
- My_playbook.yml- Used to install apache, python3, docker and DVWA servers onto Web VM's 1,2 and 3.
- ELK_playbook.yml- Used to install the Elk server.
- Filebeat_playbook.yml- Used to install Filebeat onto DVWA and ELK servers.  
- Config_Filebteat.yml- Used to configure Filebeat onto DVWA and ELK servers.
- Metricbeat.yml- Used to configure Metricbeat onto DVWA and Elk servers.

This document contains the following details:
- Description of the Topology
- Access Policies
- ELK Configuration
  - Beats in Use
  - Machines Being Monitored
- How to Use the Ansible Build

Description of the Topology

The main purpose of this network is to expose a load-balanced and monitored instance of DVWA, the D*mn Vulnerable Web Application.

Load balancing ensures the application will be highly available , in addition to restricting access to the network.
What aspect of security do load balancers protect? Load balancing ensures redundancy of an application by providing access to multiple VM's yet restricting unauthorized access. This fulfills the availability aspect of the CIA triad.  In addition, a load balancer protects a network from Direct Denial of Service (DDoS) attacks by shifting network traffic from a local server to a cloud provider.

What is the advantage of a jump box? One advantage of a Jump Box is to have a single node for sending network or application traffic across multiple servers. It provides a Secure Admin Workstation (SAW) for accessing servers in different security zones.  

Integrating an ELK server allows users to easily monitor the vulnerable VMs for changes to the logs and system traffic.
- Filebeat monitors log files, collect events and forwards them to Elasticsearch or Logstash for indexing.
- Metricbeat collects metrics and statistics from services running on a server then outputs them to Elasticsearch or Logstash.

The configuration details of each machine may be found below.

| Name     	| Function     	| IP Address 	| Operating System     	|
|----------	|--------------	|------------	|----------------------	|
| Jump Box 	| Gateway      	| 10.0.0.4   	| Linux (ubuntu 18.04) 	|
| Web-1    	| DVWA, Docker 	| 10.0.0.5   	| Linux (ubuntu 18.04) 	|
| Web-2    	| DVWA, Docker 	| 10.0.0.6   	| Linux (ubuntu 18.04) 	|
| Web-3    	| DVWA, Docker 	| 10.0.0.7   	| Linux (ubuntu 18.04) 	|
| ELK      	| ELK Server   	| 10.1.0.4   	| Linux (ubuntu 18.04) 	|

Access Policies

The machines on the internal network are not exposed to the public Internet.

Only the Jump-Box Provisioner machine can accept connections from the Internet. Access to this machine is only allowed from the System Administrator's IP address.    

Machines within the network can only be accessed by SSH.
- The ELK server is accessible by SSH from the Jump-Box Provisioner and via HTTP with the IP address of the System Administrator, 207.255.xxx.xx.

A summary of the access policies in place can be found in the table below.

| Name     	| Publicly Accessible 	| Allowed IP Address  	|
|----------	|---------------------	|---------------------	|
| Jump Box 	| Yes                 	| 207.225.xxx.xx      	|
| Web-1    	| No                  	| 10.0.0.4            	|
| Web-2    	| No                  	| 10.0.0.4            	|
| Web-3    	| No                  	| 10.0.0.4            	|
| ELK      	| No                  	| 10.0.0.4            	|

Note: The virtual machines can only be accessed by SSH from the Jump-Box Provisioner's private IP address 10.0.0.4.  

Elk Configuration

Ansible was used to automate configuration of the ELK machine. No configuration was performed manually, which is advantageous because it allows for quick setup without having to touch each server individually.

The playbook implements the following tasks:

Install docker.io
  - name: docker.io
    apt:
      force_apt_get: yes
      update_cache: yes
      name: docker.io
      state: present

Install pip3
- name: Install pip3
   apt:
      force_apt_get: yes
      name: python3-pip
      state: present

Install Docker python module
- name: Install Docker python module
  pip:
    name: docker
    state: present

Download and launch a docker web container (image sebp/elk) to published ports (5044, 5601 and 9200)
- name: download and launch a docker web container
   docker_container:
     name: elk
     image: sebp/elk:761
     state: started
     restart_policy: always
     published_ports:
       - "5601:5601"
       - "9200:9200"
       - "5044:5044"

Enable docker service
- name: Enable docker service
    systemd:
      name: docker
      enabled: yes

Increase virtual memory  
- name: Use more memory
    sysctl:
      name: vm.max_map_count
      value: '262144'
      state: present
      reload: yes

The following screenshot displays the result of running `docker ps` after successfully configuring the ELK instance.

![image](https://user-images.githubusercontent.com/73409624/112742458-5c177c80-8f54-11eb-8b1d-52fafaf9d629.png)

Target Machines & Beats
This ELK server is configured to monitor the following machines:
- Web-1 10.0.0.5
- Web-2 10.0.0.6
- Web-3 10.0.0.7

We have installed the following Beats on these machines:
- Filebeat
- Metricbeat

These Beats allow us to collect the following information from each machine:
- Filebeat collects log files from servers like Apache, nginx, MySQL to forward to Logstash for processing or to index in Elasticsearch. These files can be viewed on Kibana. They provide information on the server like requests from other computers that result in response and actions.
- Metricbeat collects server metrics like CPU, memory usage and network statistics, which are used to monitor system health. The data is sent to Elasticsearch which can be viewed on Kibana.

Using the Playbook
In order to use the playbook, you will need to have an Ansible control node already configured. Assuming you have such a control node provisioned, plus an ELK server container up and running. SSH into the control node and follow the steps below:

Step 1. Configure the filebeat-configuration.yml file
- Navigate to http://13.82.85.204:5601:5601/app/kibana.
- Click on Add Log data.
- Choose System Logs.
- Click on the DEB tab under Getting Started to view correct Linus Filebeat installation instructions.
- Copy the filebeat-configuration.yml file to /etc/ansible/roles/ directory.
- Update the filebeat-configuration.yml file to include the following:
    - Scroll to line #1106 and replace the IP address with the IP address of the ELK machine.
    - Scroll to line #1806 and replace IP address with the IP address of ELK machine.
    - Save the file in /etc/ansible/files/filebeat-config.yml.

~/OneDRive/Documents/Elk-Stack-Project/Ansible/Config_Filebteat.yml

- Run the playbook with the command: ansible-playbook filebeat-configuration.yml.

Step 2. Create filebeat-playbook.yml
- Create a filebeat-playbook.yml in the /etc/ansible/roles/ directory.
- Vi or Nano can be used as text editors for writing the playbook.
- Include the .deb file using the command: dpkg -i filebeat-7.4.0-amd64.deb
- Copy the filebeat to /etc/filebeat/filebeat.yml
- Include the following commands:
   - filebeat modules enable system
   - filebeat setup
   - service filebeat start

~/OneDRive/Documents/Elk-Stack-Project/Ansible/Filebeat_playbook.yml

- Run the playbook with the command: ansible-playbook filebeat_playbook.yml

Step 3. Verify Filebeat configuration was successful.
- Navigate to http://13.82.85.204:5601/app/kibana to check that the installation worked as expected.
- Click on Add Log data.
- Choose System Logs.
- Click on DEB tab and navigate to Module status.
- Click on Check Data.
- Scroll to the bottom of the page and click Verify Incoming Data.

~/oneDRive/Documents/Elk-Stack-Project/images/'Filebeat success.JPG'

Metricbeat was installed following the instructions provided on Kibana's website for Docker metrics. The configuration file was edited to include the ELK server. In addition, an ansible playlist was created to install and launch metricbeat. The success of the playbook was verified by checking the module status under docker metrics in Kibana.

~/OneDRive/Documents/Elk-Stack-Project/Ansible/Config_Metricbeat.yml
~/OneDRive/Documents/Elk-Stack-Project/Ansible/Metricbeat.yml
~/OneDRive/Documents/Elk-Stack-Project/images/'Metricbeat success.JPG'


Answer the following questions to fill in the blanks:
- Which file is the playbook? The playbook is filebeat-playbook.yml.
- Where do you copy it? It is copied to /etc/ansible/roles directory.
- Which file do you update to make Ansible run the playbook on a specific machine? How do I specify which machine to install the ELK server on versus which to install Filebeat on? The hosts file is updated with the private IP address of the web and ELK servers, this specifies which machine to install. The ansbile files are updated by selecting the group to run the playbook on. This determines which machines will have the ELK server or Filebeat.

~/OneDRive/Documents/Elk-Stack-Project/Ansible/hosts

- Which URL do you navigate to in order to check that the ELK server is running? http://13.82.85.204:5601/app/kibana.
