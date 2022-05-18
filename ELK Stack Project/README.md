# README

## Automated ELK Stack Deployment

The files in this repository were used to configure the network depicted below.

![ELK Stack Network Diagram](images/elk_stack-network_diagram.png)

These files have been tested and used to generate a live ELK deployment on Azure. They can be used to recreate the entire deployment pictured above.

Alternatively, select portions of the [webvm-playbook.yml](ansible/webvm-playbook.yml) file may be used to install only certain pieces of it, such as Filebeat.

This document contains the following details:
- Description of the Topology
- Access Policies
- ELK Configuration
  - Beats in use
  - Machines being monitored
- How to Use the Ansible Build

---

### Description of the Topology

The main purpose of this network is to expose a load-balanced and monitored instance of DVWA (Damn Vulnerable Web Application).

Load balancing ensures that the application will be highly responsive, and improve availability. In addition, the jump box is used to restrict access to the network.

Integrating an ELK server allows users to easily monitor the vulnerable VMs for changes to the log data and system metrics.

The configuration details of each machine may be found below.

| Name | Function | IP Address | Operating System |
|---|---|---|---|
| Jump-Box-Provisioner | Gateway | 10.0.0.4, 20.190.96.155 | Linux |
| Elk-VM-1 | ELK Server | 10.1.0.4, 20.211.35.196 | Linux |
| Web-1 | Web Server | 10.0.0.5 | Linux |
| Web-2 | Web Server | 10.0.0.6 | Linux |
| Web-3 | Web Server | 10.0.0.7 | Linux |

### Access Policies

The machines on the internal network are not exposed to the public Internet. 

Only the _Jump-Box-Provisioner_ and _Elk-VM-1_ machines can accept connections from the Internet. Access to _Jump-Box-Provisioner_ is only allowed from the local workstation (SSH), and access to _Elk-VM-1_ is restriced to _Jump-Box-Provisioner_ (SSH) and the local workstation (TCP port 5601).

Machines within the network can only be accessed by _Jump-Box-Provisioner_ (10.0.0.4).

A summary of the access policies in place can be found in the table below.

| Name | Publicly Accessible | Allowed IP Addresses |
|---|---|---|
| Jump-Box-Provisioner | Yes | Local Workstation |
| Elk-VM-1 | Yes | 10.0.0.4, Local Workstation |
| Web-1 | No | 10.0.0.4 |
| Web-2 | No | 10.0.0.4 |
| Web-3 | No | 10.0.0.4 |

### Elk Configuration

Ansible was used to automate configuration of the ELK machine. No configuration was performed manually, which is advantageous because it allows machines and servers to be reconfigured quickly and easily via code.

The playbook implements the following tasks:
- increase the systems virtual memory (for Elasticsearch)
- install Docker
- install python3
- install the Docker python package
- configuration of the ELK stack container
- ensure the Docker service is started on boot

The following screenshot displays the result of running `docker ps` after successfully configuring the ELK instance.

![ELK container details](images/docker_ps_output.png)

### Target Machines & Beats

This ELK server is configured to monitor the following machines:
- Web-1, IP: 10.0.0.5
- Web-2, IP: 10.0.0.6
- Web-3, IP: 10.0.0.7

We have installed the following Beats on these machines:
- Filebeat
- Metricbeat

These Beats allow us to collect the following information from each machine:
- Filebeat: collects log data; used to centralise and monitor changes in typical, and specified, log locations
- Metricbeat: periodically collects metrics from the operating system and services running; used to centralise and monitor system metrics e.g. CPU/memory usage

### Using the Playbook

In order to use the playbook, you will need to have an Ansible control node already configured. Assuming you have such a control node provisioned: 

SSH into the control node and follow the steps below:

```bash
# Clone this repo
user@controlnode:~$ git clone https://github.com/Buffaloberry/bc-files.git

# Update files to configure your servers, and the installation of filebeat & metricbeat
user@controlnode:~$ nano bc-files/ansible/{ansible.cfg,hosts,roles/files/*.yml}
```

- File updates to be made:
  - `ansible/ansible.cfg`: change the value of `remote_user` to the username hosting your Ansible control node
  ![ansible.cfg](images/file_update-1.png)
  - `ansible/hosts`: replace the IPs under the `[webservers]` (servers to be monitored) and `[elk]` (ELK server) groups to the IP of the relevant machines
  ![hosts](images/file_update-2.png)
  - `ansible/roles/files/filebeat-config.yml`: replace the IP at lines 1105 and 1805 to the IP of your ELK server
  ![filebeat-config.yml-1](images/file_update-3.png)
  ![filebeat-config.yml-2](images/file_update-4.png)
  - `ansible/roles/files/metricbeat-config.yml`: replace the IP at lines 62 and 95 to the IP of your ELK server
  ![metricbeat-config.yml-1](images/file_update-5.png)
  ![metricbeat-config.yml-2](images/file_update-6.png)

```bash
# Start your Ansible container
user@controlnode:~$ sudo docker start [container name]

# Copy the ansible directory to /etc in the container
user@controlnode:~$ sudo docker cp bc-files/ansible/ [container name]:/etc/

# Attach to your ansible container
user@controlnode:~$ sudo docker attach [container name]

# Run the playbooks webvm-playbook.yml & elkvm-playbook.yml to set up your web and ELK servers respectively
root@containerID:~$ ansible-playbook /etc/ansible/roles/webvm-playbook.yml && ansible-playbook /etc/ansible/roles/elkvm-playbook.yml
```

- Navigate to `[public IP of ELK server]:5601/app/kibana` to check that the installation worked as expected.

![Kibana home page](images/kibana_home.png)
