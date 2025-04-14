# V-Server Setup Guide

## Project Description
This guide covers setting up secure SSH key-based access, installing and configuring Nginx, and integrating Git for code deployment.

## Table of Contents
1. [Detailed Project Description](#detailed-project-description)
2. [Prerequisites](#prerequisites)
3. [Quickstart](#quickstart)
4. [Usage](#usage)
	- [Introduction to V-Server and SSH Connection](#introduction-to-v-server-and-ssh-connection)
	   - [Connecting via SSH](#connecting-via-ssh)
	   - [Creating SSH Key for V-Server](#creating-ssh-key-for-v-server)
	   - [Transferring created SSH Key to the V-Server](#transferring-created-ssh-key-to-the-v-server)
	   - [Connecting using Public Key Without typing Password](#connecting-using-public-key-without-typing-password)
	   - [Disabling Password Authentication](#disabling-password-authentication)
	- [Working with the V-Server](#working-with-the-v-server)
	   - [Updating Package Sources](#updating-package-sources)
	   - [Installing Nginx](#installing-nginx)
	   - [Checking Nginx Service Status](#checking-nginx-service-status)
	   - [Ensuring Nginx is Running](#ensuring-nginx-is-running)
	- [Configuring an Nginx Web Server](#configuring-an-nginx-web-server)
	   - [Viewing Default Nginx Content](#viewing-default-nginx-content)
	   - [Creating an Alternative index.html](#creating-an-alternative-indexhtml)
	   - [Creating an Nginx Configuration File](#creating-an-nginx-configuration-file)
	   - [Modifying the alternate-index.html File](#modifying-the-alternate-indexhtml-file)
	   - [Restarting the Nginx Service](#restarting-the-nginx-service)
	   - [Checking Nginx Status](#checking-nginx-status)
	   - [Verifying the Configuration](#verifying-the-configuration)
	- [Configuring Git on the V-Server](#configuring-git-on-the-v-server)
	   - [Generating an SSH Key](#generating-an-ssh-key)
	   - [Adding VServer SSH Key to GitHub](#adding-vserver-ssh-key-to-github)
	   - [Cloning Git Repository](#cloning-git-repository)
	   - [Configuring Git Username and Email](#configuring-git-username-and-email)
	   - [Verifying the Configuration](#verifying-the-configuration)

# Detailed Project Description

This repository offers a step-by-step tutorial to set up a basic Ubuntu-based V-Server from scratch. You'll learn to:
- Establish a secure SSH connection using public/private key authentication
- Configure the server to disable password-based login
- Install and manage Nginx to serve a basic website
- Set up Git on the server and connect it to a GitHub repository

# Prerequisites

Before starting, make sure you have the following:
- A virtual server running Linux
- SSH access to the server
- sudo rights on your vm
- GitHub account and SSH client

# Quickstart

1. **Generate an SSH key:**
   ```sh
   ssh-keygen -t ed25519
   ```

2. **Transfer the SSH key to your server:**
   ```sh
   ssh-copy-id <your_username>@<your_vm_ip>
   ```

3. **Test the connection using the key:**
   ```sh
   ssh -i ~/.ssh/id_ed25519 <your_username>@<your_vm_ip>
   ```

4. **Disable password authentication:**
   - Edit the SSH config:
     ```sh
     sudo vi /etc/ssh/sshd_config
     ```
   - Set:
     ```sh
     PasswordAuthentication no
     ```

5. **Restart the SSH service:**
   ```sh
   sudo systemctl restart ssh.service
   ```

6. **Install Nginx:**
   ```sh
   sudo apt install nginx -y
   ```

7. **Verify Nginx is running:**
   ```sh
   systemctl status nginx
   ```

8. **Visit your server IP in the browser:**
   ```
   http://<your_vm_ip>
   ```

# Usage

## Introduction to V-Server and SSH Connection

### Connecting via SSH
To connect to the V-Server, use:
```sh
ssh <your_username>@<your_vm_ip>
```

### Creating SSH Key for V-Server
```sh
ssh-keygen -t id_ed25519
```

### Transferring created SSH Key to the V-Server
To transfer the public SSH key to the V-Server, run:
```sh
ssh-copy-id -i ~/.ssh/id_ed25519.pub <your_username>@<your_vm_ip>
```

### Connecting using SSH
Run SSH command wirh Public Key you created before:
```sh
ssh -i ~/.ssh/id_ed25519 <your_username>@<your_vm_ip>
```

### Disabling Password Authentication
Edit the SSH configuration file:
```sh
sudo vi /etc/ssh/sshd_config
```
Make sure the following line is uncommented and modified:
```sh
PasswordAuthentication no
```

#### Restarting the SSH Service
```sh
sudo systemctl restart ssh.service
```

#### Verifying Password Authentication
After logging out, the connection should not work without the public key:
```sh
ssh -o PubkeyAuthentication=no <your_username>@<your_vm_ip>
```
Expected output:
```sh
<your_username>@<your_vm_ip>: Permission denied (publickey).
```

The connection should now only work using the public key:
```sh
ssh -i ~/.ssh/id_ed25519 <your_username>@<your_vm_ip>
```

## Working with the V-Server
### Updating Package Sources
```sh
sudo apt update
```

### Installing Nginx
```sh
sudo apt install nginx -y
```

### Checking Nginx Service Status
```sh
systemctl status nginx.service
```
Expected output:
```sh
Active: active (running) since Wed 2025-04-02 19:00:21 UTC; 1min 33s ago
```

### Ensuring Nginx is Running
Visit the server's IP address in a browser:
```
http://<your_vm_ip>
```

## Configuring an Nginx Web Server

### Viewing Default Nginx Content
Before making changes, the default content displayed at `http://<your_vm_ip>` is located in:
```sh
sudo cat /var/www/html/index.nginx-debian.html
```

### Creating an Alternative `index.html`
To set up an alternative index page, create the necessary directories and files:
```sh
sudo mkdir /var/www/alternatives/
sudo touch /var/www/alternatives/alternate-index.html
```

### Creating an Nginx Configuration File
Next, create a new configuration file for the alternative site:
```sh
sudo vi /etc/nginx/sites-enabled/alternatives
```
The file should contain the following configuration:
```nginx
server {
	listen 8081;
	listen [::]:8081;

	root /var/www/alternatives;
	index alternate-index.html;

	location / {
		try_files $uri $uri/ =404;
	}
}
```

### Modifying the `alternate-index.html` File
Edit the newly created alternative index file:
```sh
sudo vi /var/www/alternatives/alternate-index.html
```
Add the following content:
```html
<!doctype html>
<html>
	<head>
		<meta charset="utf-8">
		<title>Hello, nginx!</title>
	</head>
	<body>
		<h1>Hello, nginx!</h1>
		<p>I have just configured our first nginx web server on Ubuntu Server!</p>
	</body>
</html>
```

## Configuring Git on the V-Server

### Generating an SSH Key
To generate a new SSH key, run:
```sh
ssh-keygen -t ed25519
```

### Adding VServer SSH Key to GitHub
1. Copy the generated SSH key:
   ```sh
   cat ~/.ssh/id_ed25519.pub
   ```
2. Go to [GitHub SSH Key Settings](https://github.com/settings/keys).
3. Click **New SSH Key**, paste the key, and save it.

### Cloning Git Repository
Once the SSH key is added, clone the repository:
```sh
git clone git@github.com:vkebkal/v-server-setup.git
```

### Configuring Git Username and Email
Set your Git username and email for this repository:
```sh
git config user.name "<your_surname your_name>"
git config user.email "<your_email>"
```

### Verifying the Configuration
```sh
git config --local --list
```
