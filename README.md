## Automated ELK Stack Deployment

The files in this repository were used to configure the network depicted below. 

![Diagram](https://github.com/L-A-C-Git/Elk-Project/blob/master/images/Final.png)

These files have been tested and used to generate a live ELK deployment on Azure. They can be used to either recreate the entire deployment pictured above. Alternatively, select portions of the YAML file may be used to install only certain pieces of it, such as Filebeat.

[install-elk](https://github.com/L-A-C-Git/Elk-Project/blob/master/Playbooks/install-elk.yml)

```
---
  - name: Configure Elk VM with Docker
    hosts: elk
    remote_user: Logan
    become: true
    tasks:
     # Use apt module
    - name: Install docker.io
      apt:
        update_cache: yes
        name: docker.io
        state: present

     # Use apt module
    - name: Install pip3
      apt:
        force_apt_get: yes
        name: python3-pip
        state: present

     # Use pip module
    - name: Install Docker python module
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
        value: "262144"
        state: present
        reload: yes

     # Use docker_container module
    - name: download and launch a docker elk container
      docker_container:
        name: elk
        image: sebp/elk:761
        state: started
        restart_policy: always
        published_ports:
          - 5601:5601
          - 9200:9200
          - 5044:5044

      # Restart docker for Kibana
    - name: Enable docker service
      systemd:
        name: docker
        enabled: yes
```

This document contains the following details:
- Description of the Topology
- Access Policies
- ELK Configuration
  - Beats in Use
  - Machines Being Monitored
- How to Use the Ansible Build

## Description of the Topology

The main purpose of this network is to expose a load-balanced and monitored instance of DVWA, the D*mn Vulnerable Web Application.

Load balancing ensures that the application will be highly available, in addition to restricting in-bound access to the network.

What aspect of security do load balancers protect? 

- A load balancer intelligently distributes traffic from clients across multiple servers without the clients having to understand how many servers are in use or how they are configured. Because the load balancer sits between the clients and the servers it can enhance the user experience by providing additional security, performance, resilience and simplify scaling your website.

What is the advantage of a jump box?

- A jump box is a secure computer that all admins first connect to before launching any administrative task or use as an origination point to connect to other servers or untrusted environments.

Integrating an ELK server allows users to easily monitor the vulnerable VMs for changes to the jumpbox and system network

What does Filebeat watch for?

- Filebeat watches for changes to files on the machine.

What does Metricbeat record?

- Metricbeat collects metrics from the operating system and from services running on the server.

The configuration details of each machine may be found below.

| Name       | Function   | IP Address | Operating System |
|------------|------------|------------|------------------|
| Jump-Box-Provisioner   | Gateway    | 10.0.0.7   | Linux            |
| Web-1      | Webserver  | 10.0.0.5   | Linux            |
| Web-2      | Webserver  | 10.0.0.6   | Linux            |
| ELK-VM | Monitoring | 10.1.0.4   | Linux            |

## Access Policies

The machines on the internal network are not exposed to the public Internet. 

Only the jump box provisioner machine can accept connections from the Internet. Access to this machine is only allowed from the following IP addresses:

- 71.135.133.0/24 via SSH to the Jump-Box-Provisioner Public IP of 52.249.181.84

Machines within the network can only be accessed by jump box provisioner.

Which machine did you allow to access your ELK VM? What was its IP address?

- Jump-Box-Provisioner
  - Public IP: 52.249.181.84
  - Private IP: 10.0.0.7
  
A summary of the access policies in place can be found in the table below.

| Name       | Publicly Accessible | Allowed IP Addresses |
|------------|---------------------|----------------------|
| Jump-Box-Provisioner   | Yes                 | 71.135.133.0/24      |
| Web-1      | No                  | 10.0.0.7 |
| Web-2      | No                  | 10.0.0.7  |
| ELK-VM | No                  | 10.0.0.7 |


## Elk Configuration

Ansible was used to automate configuration of the ELK machine. No configuration was performed manually, which is advantageous for the following reasons:

- Free: Ansible is an open-source tool.
- Very simple to set up and use: No special coding skills are necessary to use Ansible’s playbooks (more on playbooks later).
- Powerful: Ansible lets you model even highly complex IT workflows.
- Flexible: You can orchestrate the entire application environment no matter where it’s deployed. You can also customize it based on your needs.
- Agentless: You don’t need to install any other software or firewall ports on the client systems you want to automate. You also don’t have to set up a separate management structure.
- Efficient: Because you don’t need to install any extra software, there’s more room for application resources on your server.

The playbook implements the following tasks:

- Install docker.io
- Install pip3
- Install Docker python module
- Increase virtual memory
- Download and launch a docker

The following screenshot displays the result of running `docker ps` after successfully configuring the ELK instance.

![Docker PS Output](https://github.com/L-A-C-Git/Elk-Project/blob/master/images/Docker%20PS.jpg)

## Target Machines & Beats

This ELK server is configured to monitor the following machines:

| Name   | IP Address |
|--------|------------|
| Web-1  | 10.0.0.5   |
| Web-2  | 10.0.0.6   |

We have installed the following Beats on these machines:

- Microbeats.

These Beats allow us to collect the following information from each machine:

- Filebeat: Collects data about the file system.
- Metricbeat: Collects machine metrics, such as uptime.

## Using the Playbook
In order to use the playbook, you will need to have an Ansible control node already configured. Assuming you have such a control node provisioned: 

SSH into the control node and follow the steps below:

- Copy the yml files to the Ansible control node. The yml files can be found here: [install-elk](https://github.com/L-A-C-Git/Elk-Project/blob/master/Playbooks/install-elk.yml) | [filebeat-playbook](https://github.com/L-A-C-Git/Elk-Project/blob/master/Playbooks/filebeat-playbook.yml) | [metricbeat-playbook](https://github.com/L-A-C-Git/Elk-Project/blob/master/Playbooks/metricbeat-playbook.yml)

```
$ cd /etc/ansible
mkdir files
# Clone Repository
$ git clone https://github.com/L-A-C-Git/Elk-Project
# Move yml files into `/etc/ansible`
$ cp Elk-Project/Playbooks/* files
```

- Update the hosts file to include webservers and elk.

```
$ cd /etc/ansible
$ nano hosts
# Ex 2: A collection of hosts belonging to the 'webservers' group
[webservers]
10.0.0.5 ansible_python_interpreter=/usr/bin/python3
10.0.0.6 ansible_python_interpreter=/usr/bin/python3

[elk]
10.1.0.4 ansible_python_interpreter=/usr/bin/python3
```

- Run the install-elk.yml file.

```
cd /etc/ansible
 $ ansible-playbook install-elk.yml
```

- Ensure a NSG rule exists allowing TCP traffic over port 5601.

- Check that the ELK server is running: http://[ELK-Public-IP]:5601/app/kibana#/home

## Install Filebeat

- Open your ELK server homepage.
  - Click on Add Log Data.
  - Choose System Logs.
  - Click on the DEB tab under Getting Started to view the correct Linux Filebeat installation instructions.
  
- In your Ansible container run ```curl https://gist.githubusercontent.com/slape/5cc350109583af6cbe577bbcc0710c93/raw/eca603b72586fbe148c11f9c87bf96a63cb25760/Filebeat > /etc/ansible/filebeat-config.yml```

- Edit the filebeat-config.yml
  - Scroll to line #1106 and replace the IP address with the IP address of your ELK machine.
    ```
    output.elasticsearch:
    hosts: ["10.1.0.4:9200"]
    username: "elastic"
    password: "changeme"
    ```

  - Scroll to line #1806 and replace the IP address with the IP address of your ELK machine.
    ```
    setup.kibana:
    host: "10.1.0.4:5601"
    ```

- In your Ansible container run the following playbook to install Filebeat:

```
cd /etc/ansible/files
$ ansible-playbook filebeat-playbook.yml
```

- To confirm that the ELK stack is receiving logs, navigate back to the Filebeat installation page on the ELK server GUI, scroll to Step 5: Module Status and click Check Data.

## Install Metricbeat

- Open your ELK server homepage.
  - Click on Add Metric Data.
  - Click Docker Metrics.
  - Click the DEB tab under Getting Started for the correct Linux instructions.

- In your Ansible container run ```curl https://gist.githubusercontent.com/slape/58541585cc1886d2e26cd8be557ce04c/raw/0ce2c7e744c54513616966affb5e9d96f5e12f73/metricbeat > /etc/ansible/metricbeat-config.yml```

- Edit the metricbeat-config.yml
```
$ cd /etc/ansible
$ nano metricbeat-config.yml
```

  - Scroll to line #62 and replace the IP address with the IP address of your ELK machine
  ```
  # This requires a Kibana endpoint configuration.
  setup.kibana:
  host: "10.1.0.4:5601"
  ```

  - Scroll to line #95 and replace the IP address with the IP address of your ELK machine.
  ```
  output.elasticsearch:
  # Array of hosts to connect to.
  hosts: ["10.1.0.4:9200"]
  username: "elastic"
  password: "changeme"
  ```

- In your Ansible container run the following playbook to install Metricbeat:

```
cd /etc/ansible/files
$ ansible-playbook metricbeat-playbook.yml
```

- To verify that your playbook works as expected, on the Metricbeat installation page in the ELK server GUI, scroll to Step 5: Module Status and click Check Data.
