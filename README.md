## Automated ELK Stack Deployment

The files in this repository were used to configure the network depicted below.

![This would need to be updated.](Images/cloud-azure-wk11.png?raw=true "Cloud Infra Diagram")

These files have been tested and used to generate a live ELK deployment on Azure. They can be used to either recreate the entire deployment pictured above. Alternatively, select portions of the elk-playbook.yml file may be used to install ELK Stack. There are separate files added below for filebeat & metricbeat.

```
---
- name: Configure Elk VM with Docker
  hosts: elk
  remote_user: azadmin
  become: true
  tasks:
    # Use apt module
    - name: Install docker.io
      apt:
        update_cache: yes
        force_apt_get: yes
        name: docker.io
        state: present

      # Use apt module
    - name: Install python3-pip
      apt:
        force_apt_get: yes
        name: python3-pip
        state: present

      # Use pip module (It will default to pip3)
    - name: Install Docker module
      pip:
        name: docker
        state: present

      # Use command module
    - name: Increase virtual memory
      command: sysctl -w vm.max_map_count=262144

      # Use sysctl module
    - name: Use more memory
      sysctl:
        name: vm.max_map_count
        value: 524288
        state: present
        reload: yes

      # Use docker_container module
    - name: download and launch a docker elk container
      docker_container:
        name: elk
        image: sebp/elk:761
        state: started
        restart_policy: always
        # Please list the ports that ELK runs on
        published_ports:
          - 5601:5601
          - 9200:9200
          - 5044:5044

      # Use systemd module
    - name: Enable service docker on boot
      systemd:
        name: docker
        enabled: yes

```

Sample Filebeat Installation

```
---
- name: Configure filebeat on all VMs
  hosts: webservers
  remote_user: azadmin
  become: true
  tasks:
    # Using commnds
    - name: download filebeat
      command: curl -L -O https://artifacts.elastic.co/downloads/beats/filebeat/filebeat-7.6.1-amd64.deb

    - name: install filebeat
      command: sudo dpkg -i filebeat-7.6.1-amd64.deb

    - name: copy filebeatconfig
      ansible.builtin.copy:
        src: /etc/ansible/files/filebeat-config.yml
        dest: /etc/filebeat/filebeat.yml

    - name: run filebeat start commands
      command: sudo filebeat modules enable system

    - name: run filebeat start commands-2
      command: sudo filebeat setup

    - name: run filebeat start commands-3
      command: sudo service filebeat start

    - name: enable filebeat on boot
      systemd:
        name: filebeat
        enabled: yes

```

  

This document contains the following details:
- Description of the Topologu
- Access Policies
- ELK Configuration
  - Beats in Use
  - Machines Being Monitored
- How to Use the Ansible Build


### Description of the Topology

The main purpose of this network is to expose a load-balanced and monitored instance of DVWA, the D*mn Vulnerable Web Application.

Load balancing ensures that the application will be highly scalable, in addition to restricting access to the network.

What aspect of security do load balancers protect? 
> Loadbalancers provide a single access point for the web apps, which allow access only on port 80(http) or 443(https). Apart from uniformly distributing load across webservers, loadbalancers allow only specific ports from internet to the webservers.

What is the advantage of a jump box?
> Jump box provide a single access point for all virtual machines. This can help in controlling and tracking who is accessing the virtual machines. If we don't have a jump box we would need to provide access to all VMs to individual ips from the outside/inside network, which is difficult.

Integrating an ELK server allows users to easily monitor the vulnerable VMs for changes to the application and system logs.

What does Filebeat watch for?
> Filebeat is used to send the logs from virtual machines/servers to a central ELK stack. Filebeat can be run on docker and work as an agent for log aggregation on the virual machines. 

What does Metricbeat record?
> Metricbeat helps to record different metrics from the OS on which they are installed and working. These can then be configured to send the metric details to the ELK Stack for aggregation.

The configuration details of each machine may be found below.

| Name     | Function | IP Address | Operating System |
|----------|----------|------------|------------------|
| Jump Box | Gateway  | 10.0.0.1   | Linux            |
| Web VM1  | Web-app  | 10.0.0.6   | Linux            |
| Web VM2  | Web-app  | 10.0.0.6   | Linux            |
| ELK VM   | ELK Stk  | 10.1.0.5   | Linux            |

### Access Policies

The machines on the internal network are not exposed to the public Internet. 

Only the Jumpbox machine can accept connections from the Internet, with port 22 to be accessible from a IP(s). Access to this machine is only allowed from the following IP addresses:

Whitelisted IP addresses
> 119.18.1.177/32 of the user laptop.

Machines within the network can only be accessed by Jumbo Box and all other VMs within the network.

Which machine did you allow to access your ELK VM? What was its IP address?
> The ELK stack VM was allowed access from the Virtual Network of Web & jump box VM's. Hence its accessible from the Jumpbox. Also, The ELK stack VM is allowed public access on port 5601 from internet to access the kibana dashboard.

A summary of the access policies in place can be found in the table below.

| Name      | Publicly Accessible | Allowed IP Addresses | Details       |
|-----------|---------------------|----------------------|---------------|
| Jump Box  | Yes                 | 119.18.1.177/32 :22  | User laptop   |
| Web VM1   | No                  | IP's with Web VN     | Web app VM1   |
| Web VM2   | No                  | IP's with Web VN     | Web app VM2   |
| ELK Stack | Yes                 | 119.18.1.177/32:5601 | ELK Stack     |
|           |                     |                      |               |

### Elk Configuration

Ansible was used to automate configuration of the ELK machine. No configuration was performed manually, which is advantageous because...
- _TODO: What is the main advantage of automating configuration with Ansible?_

The playbook implements the following tasks:
- _TODO: In 3-5 bullets, explain the steps of the ELK installation play. E.g., install Docker; download image; etc._
- ...
- ...

The following screenshot displays the result of running `docker ps` after successfully configuring the ELK instance.

![TODO: Update the path with the name of your screenshot of docker ps output](Images/docker_ps_output.png)

### Target Machines & Beats
This ELK server is configured to monitor the following machines:
- _TODO: List the IP addresses of the machines you are monitoring_

We have installed the following Beats on these machines:
- _TODO: Specify which Beats you successfully installed_

These Beats allow us to collect the following information from each machine:
- _TODO: In 1-2 sentences, explain what kind of data each beat collects, and provide 1 example of what you expect to see. E.g., `Winlogbeat` collects Windows logs, which we use to track user logon events, etc._

### Using the Playbook
In order to use the playbook, you will need to have an Ansible control node already configured. Assuming you have such a control node provisioned: 

SSH into the control node and follow the steps below:
- Copy the _____ file to _____.
- Update the _____ file to include...
- Run the playbook, and navigate to ____ to check that the installation worked as expected.

_TODO: Answer the following questions to fill in the blanks:_
- _Which file is the playbook? Where do you copy it?_
- _Which file do you update to make Ansible run the playbook on a specific machine? How do I specify which machine to install the ELK server on versus which to install Filebeat on?_
- _Which URL do you navigate to in order to check that the ELK server is running?

_As a **Bonus**, provide the specific commands the user will need to run to download the playbook, update the files, etc._