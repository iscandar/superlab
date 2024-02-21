
Introduction to SuperLab: A Virtual Laboratory with Docker
==========================================================

Project Objective
-----------------

SuperLab is designed to create an advanced computer laboratory environment within a virtual machine, leveraging Docker to manage various applications and services. Utilizing a VPN, specifically WireGuard in this instance, the lab is made easily accessible, providing an educational platform to explore and learn about modern technologies in both home and enterprise settings. This initiative aims to simplify the complex process of setting up and managing a multifaceted IT environment, ensuring that learners and professionals alike can benefit from a hands-on experience with cutting-edge technologies.

Technologies Employed
---------------------

*   **Operating System**: Ubuntu Server 22.04 on VirtualBox, chosen for its stability and widespread support within the community.
*   **Firewall**: Firewalld, selected for its Docker compatibility and ease of use in managing network security.
*   **Containers and Orchestration**: Docker and Docker Compose, for their efficiency in deploying and managing multi-container applications.
*   **Version Control**: Git, to ensure that all project changes are tracked and managed efficiently.
*   **Applications**: Categorized into Administration, Registry, Monitoring, AI, and DevOps to demonstrate a wide range of use cases.

### Application Examples by Category

*   **Administration**: Tools like Unbound, WireGuard, and Portainer to streamline network management and security.
*   **Registry**: Solutions such as Gitea, Postgres, and MariaDB for data storage and version control.
*   **Monitoring**: Technologies like InfluxDB, Grafana, and Loki for real-time data monitoring and analytics.
*   **AI**: Innovative platforms such as Ollama and Ollama WebUI for exploring artificial intelligence applications.
*   **DevOps**: Critical tools including Code Server and Jenkins for continuous integration and deployment processes.

Network Architecture
--------------------

The project utilizes dual network cards: one for simulating a "public IP" and another for the VirtualBox host, both configured with netplan and a static IP. The Docker Compose configuration outlined below showcases the internal network structure:

yamlCopy code

`networks:   administration-subnet:     driver: bridge     name: administration-subnet     ipam:       config:         - subnet: "100.100.100.0/24"           gateway: "100.100.100.1"`

This setup, detailed in the `administration-compose.yaml` file, facilitates the interconnection of various services and applications, ensuring seamless communication across the virtual lab environment.

Tutorial and Notes
------------------

### 1\. Environment Setup

#### System Update and OpenSSH and Firewalld Installation

Ensure your system is up-to-date and secure by installing essential networking and security packages.

bashCopy code

`sudo apt update sudo apt install openssh-server openssh-client sudo apt install firewalld -y`

#### Firewalld and SSH Configuration

Configure Firewalld and SSH to enhance your system's security, including custom SSH port setup.

bashCopy code

`sudo firewall-cmd --list-all sudo firewall-cmd --get-services sudo firewall-cmd --add-port=7777/tcp --permanent`

SSH configuration for certificate-based authentication and password access disablement.

bashCopy code

`sudo cp /etc/ssh/sshd_config /etc/ssh/sshd_config.original sudo chmod a-w /etc/ssh/sshd_config.original sudo vi /etc/ssh/sshd_config  # Modify the following lines Port 7777 PermitRootLogin no PubkeyAuthentication yes AuthorizedKeysFile %h/.ssh/authorized_keys.pub PasswordAuthentication no`

#### SSH Key Generation and Configuration

Securely generate and configure SSH keys for both primary and secondary users.

bashCopy code

`sudo ssh-keygen -A # Generate keys for server users sudo ssh-keygen -t rsa -b 4096 -N "" -f /home/username/.ssh/authorized_keys # For secondary users sudo mkdir -p /home/username-dev/.ssh/ sudo chown -R username-dev:username-dev /home/username-dev sudo ssh-keygen -t rsa -N "" -f /home/username-dev/.ssh/authorized_keys`

### 2\. Docker Installation and Service Configuration

#### Removing Old Versions and Installation

Transition smoothly to Docker by removing any pre-existing versions and setting up the official Docker repository for the latest updates.

bashCopy code

`# Remove old versions for pkg in docker.io docker-doc docker-compose docker-compose-v2 podman-docker containerd runc; do   sudo apt-get remove $pkg; done # Add Docker's official repository and install sudo apt-get update sudo apt-get install ca-certificates curl sudo install -m 0755 -d /etc/apt/keyrings sudo curl -fsSL https://download.docker.com/linux/ubuntu/gpg -o /etc/apt/keyrings/docker.asc sudo chmod a+r /etc/apt/keyrings/docker.asc echo "`


### 3\. Monitoring Configuration and Permission Fixing

For Grafana:

bashCopy code

`sudo chown -R 472:472 ./volumes/grafana_data`



### 4\. Security and Protection

Security is a crucial aspect when setting up a laboratory environment accessible over the internet. Below are key steps to fortify the security of your SuperLab.

### Installing Fail2Ban

Fail2Ban plays a critical role in protecting your server from intrusion attempts by blocking IP addresses that exhibit patterns of attack. This tool is an essential first line of defense against automated attacks.

bashCopy code

`sudo apt-get install fail2ban -y`

To configure Fail2Ban for SSH protection, you'll need to modify or create the `jail.local` file. This customization allows you to specify settings that are tailored to your environment, enhancing the security of the SSH service.

bashCopy code

`sudo vi /etc/fail2ban/jail.local`

Add the following configuration to the file. This setup enables Fail2Ban for SSH, sets the listening port to 7777 (or whichever custom port you're using), and defines the maximum number of retries before an IP is banned.

iniCopy code

`[sshd] enabled = true port = 7777 filter = sshd logpath = /var/log/auth.log maxretry = 5`

Restarting the Fail2Ban service applies the changes, ensuring that any malicious IP addresses are promptly blocked from accessing your server.

bashCopy code

`sudo systemctl enable fail2ban sudo systemctl restart fail2ban`

### SSH Key Management

Proper management of SSH keys is fundamental to securing your server. It involves removing private keys from the host and ensuring that only necessary public keys are maintained for access. This approach minimizes the risk of unauthorized access and strengthens your server's security posture.

### Disabling SSH Access Without Certificates

To ensure that your server is accessible only through SSH keys and not passwords, you must configure the `/etc/ssh/sshd_config` file accordingly. This measure significantly reduces the risk of brute force attacks.

bashCopy code

`sudo systemctl restart ssh`

By setting `PasswordAuthentication no`, you disable password-based logins, enforcing key-based authentication. This step is crucial for securing SSH access, as it ensures that only users with the correct private key can access the server. After making this change, it's important to restart the SSH service to apply the new security settings.



SuperLab Compose
----------------
Inside each Root folder there is a README file that explains the associated content.

### TODO:

'''
- Add an automatic jenkins configuration system.
- Configure a whole chain of sample deploys.
- Add applications for processing ML (jupyter,others).
- Add an automatism system to recognize graphics cards and add information automatically in composes.
- Create scripts for automating deployment and project configuration.
- Finish the translation( Many notes are in Italian or Spanish).
'''


**Join the Innovation Train! 🚀**

Dive into a world where your ideas lead the way! Fork this project now to bring your unique vision of a lab. This isn't just about coding; it's about creating something that's yours, something that speaks to your passion and ingenuity. Whether you're looking to solve a complex problem, explore new possibilities, or simply make your mark in the digital universe, this is your platform, let's innovate together.
