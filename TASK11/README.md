# ANSIBLE CONFIGURATION MANAGEMENT – AUTOMATING PROJECTS 7 TO 10

You have completed several interesting projects so far. In Projects 7 to 10, you performed many manual operations to set up virtual servers, install and configure the required software, and deploy your web application.

This project will help you appreciate DevOps tools even more by automating most routine tasks with [Ansible Configuration Management](https://www.redhat.com/en/topics/automation/what-is-configuration-management#:~:text=Configuration%20management%20is%20a%20process,in%20a%20desired%2C%20consistent%2C%20state.&text=Managing%20IT%20system%20configurations%20involves,building%20and%20maintaining%20those%20systems.). At the same time, you will become more confident writing code in a declarative language such as [YAML](https://en.wikipedia.org/wiki/YAML).

## Ansible Client as a Jump Server (Bastion Host)

A [Jump Server](https://en.wikipedia.org/wiki/Jump_server), sometimes referred to as a [Bastion Host](https://en.wikipedia.org/wiki/Bastion_host), is an intermediary server that provides access to an internal network. In the architecture you are working on, the web servers should ideally be inside a secured network that cannot be reached directly from the Internet. This means that even DevOps engineers cannot SSH directly into the web servers; they can access them only through a jump server. This provides better security and reduces the [attack surface](https://en.wikipedia.org/wiki/Attack_surface).

In the diagram below, the Virtual Private Cloud (VPC) is divided into [two subnets](https://docs.aws.amazon.com/vpc/latest/userguide/VPC_Subnets.html). The public subnet has public IP addresses, while the private subnet is reachable only through private IP addresses.

![alt text](<IMAGE/Diagram 1.11.png>)

## Task

- Install and configure Ansible client to act as a Jump Server/Bastion Host

- Create a simple Ansible playbook to automate servers configuration

## STEP 1 – INSTALL AND CONFIGURE ANSIBLE ON AN EC2 INSTANCE

- Update the Name tag on your Jenkins EC2 instance to `Jenkins-Ansible`. We will use this server to run playbooks.

- In your GitHub account, create a new repository named <span style="color:yellow">ansible-config-mgt</span>.

- Install Ansible

```bash
sudo apt update
```

```bash
sudo apt install ansible
```

- Check your Ansible version by running `ansible --version`.

![alt text](<IMAGE/TASK11 [1].png>)

### Configure Jenkins to archive repository contents

Configure a Jenkins build job to save your repository contents every time you make a change. This will reinforce the Jenkins configuration skills acquired in [Project 9](https://github.com/NduClinton/DEVOPS_PROJECT/tree/main/TASK9).

- Create a new Freestyle project named `ansible` in Jenkins and point it to your `ansible-config-mgt` repository.

- Configure a webhook in GitHub to trigger the `ansible` build.

- Configure a post-build action to archive all (`**`) files, as you did in Project 9.

![alt text](<IMAGE/TASK11 [2].png>)

![alt text](<IMAGE/TASK11 [3].png>)

Test your setup by making a change to the `README.md` file in the `main` branch. Make sure that the build starts automatically and Jenkins saves the files (build artifacts) in the following folder:

`ls /var/lib/jenkins/jobs/ansible/builds/<build_number>/archive/`

**Note:** Configure the Jenkins project to execute only for the `main` branch.

Now your setup will look like this:

![alt text](<IMAGE/Diagram 2.11.png>)

**Tip** Every time you stop/start your Jenkins-Ansible server – you have to reconfigure GitHub webhook to a new IP address, in order to avoid it, it makes sense to allocate an [Elastic IP](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/elastic-ip-addresses-eip.html) to your Jenkins-Ansible server (you have done it before to your LB server in [Project 10](https://github.com/NduClinton/DEVOPS_PROJECT/tree/main/TASK10)). Note that Elastic IP is free only when it is being allocated to an EC2 Instance, so do not forget to release Elastic IP once you terminate your EC2 Instance.

## STEP 2 – Prepare your development environment using Visual Studio Code

- Install [Visual Studio Code (VSC)](https://en.wikipedia.org/wiki/Visual_Studio_Code), you can get it [here](https://code.visualstudio.com/download).

- After you have successfully installed VSC, Clone down your `ansible-config-mgt` repo branch

```bash
git clone <ansible-config-mgt repo link>
```

## STEP 3 – BEGIN ANSIBLE DEVELOPMENT

- In your `ansible-config-mgt` GitHub repository, create a new branch that will be used for development of a new feature.

- Checkout the newly created feature branch to your local machine and start building your code and directory structure

- Create a directory and name it **playbooks** – it will be used to store all your playbook files.

- Create a directory and name it **inventory** – it will be used to keep your hosts organised.

- Within the *playbooks* folder, create your first playbook, and name it `common.yml`

- Within the *inventory* folder, create an inventory file (`.yml`) for each environment (*Development, Staging Testing and Production*) `dev`, `staging`, `uat`, and `prod` respectively.

![alt text](<IMAGE/TASK11 [4].png>)

## STEP 4 – SET UP ANSIBLE INVENTORY

Setup **SSH agent** and connect VS Code to your `Jenkins-Ansible` instance

- Run the command below on your terminal(Powershell) in VS code to check if you have OpenSSH is installed and running:

```bash
Get-WindowsCapability -online | where-Object Name -like "OpenSSH*"
```

- If it shows not installed or Not present, then you have to install it. Run the commands below:

```bash
Add-WindowsCapability -Online -Name OpenSSH.Client~~~~0.0.1.0
Add-WindowsCapability -Online -Name OpenSSH.Server~~~~0.0.1.0
```

Then run;

```bash
dism /online /Add-Capability /CapabilityName:OpenSSH.Server~~~~0.0.1.0
```

- Start and configure OpenSSH

```bash
Start-Service sshd
Set-Service -Name sshd -StartupType 'Automatic'
Get-Service sshd
```

```bash
if (!(Get-NetFirewallRule -Name "OpenSSH-Server-In-TCP" -ErrorAction SilentlyContinue)) {
    Write-Output "Firewall Rule 'OpenSSH-Server-In-TCP' does not exist, creating it..."
    New-NetFirewallRule -Name 'OpenSSH-Server-In-TCP' -DisplayName 'OpenSSH Server (sshd)' -Enabled True -Direction Inbound -Protocol TCP -Action Allow -LocalPort 22
} else {
    Write-Output "Firewall rule 'OpenSSH-Server-In-TCP' has been created and exists."
}
```

- Run the command below to create your private key, if you don't have one

```bash
ssh-keygen -t ed25519
```

- Add your private key to SSH Agent

Run:

```bash
ssh-add $env:USERPROFILE\.ssh\id_ed25519
```

Then verify:

```bash
ssh-add -l
```

- Get your public key and copy it

```bash
Get-Content $env:USERPROFILE\.ssh\id_ed25519.pub
```

- SSH into your `Jenkins-Ansible` server, once inside run;

```bash
mkdir -p ~/.ssh
chmod 700 ~/.ssh
nano ~/.ssh/authorized_keys
```

- Paste your public key inside the file and save with `Ctrl + X`

- Then run;

```bash
chmod 600 ~/.ssh/authorized_keys
```

**Note:** You can do all of these commands inside your VScode terminal.

- Now, ssh into your `Jenkins-Ansible` server using ssh-agent

```bash
ssh -A ubuntu@Jenkins-Ansible_ip
```

- Update your `inventory/dev.yml` file with this snippet of code:

> *You can do this from your VSc README.md,then you save and push to your github repo or inside the server terminal. Ensure you input the Private_IP address of all servers.*

```bash
[nfs]
<NFS-Server-Private-IP-Address> ansible_ssh_user='ec2-user'

[webservers]
<Web-Server1-Private-IP-Address> ansible_ssh_user='ec2-user'
<Web-Server2-Private-IP-Address> ansible_ssh_user='ec2-user'

[db]
<Database-Private-IP-Address> ansible_ssh_user='ec2-user' 

[lb]
<Load-Balancer-Private-IP-Address> ansible_ssh_user='ubuntu'

[dev:children]
nfs 
webservers 
db 
lb 

[dev:vars] 
ansible_user=ec2-user
ansible_ssh_private_key_file=/home/ubuntu/YOUR_KEYPAIR.pem 

[lb:vars] 
ansible_user=ubuntu
```

![alt text](<IMAGE/TASK11 [5].png>)

- To run from terminal, navigate to the actual Ansible project directory:

```bash
cd /var/lib/jenkins/workspace/Ansible
```

run;

```bash
ls -la 
ls -la inventory
```

- After verifying that `inventory/dev.yml` is in that directory, you need to copy and paste the snippet of code inside it. Run;

```bash
nano inventory/dev.yml
```

To Save and Exit:
`Ctrl + O`, click `ENTER` and `Ctrl + X`

## STEP 5 – CREATE A COMMON PLAYBOOK

- Update your `playbook/common.yml` file with the following code:

```bash
- name: update web, nfs and db servers
  hosts: webservers, nfs, db
  remote_user: ec2-user
  become: yes
  become_user: root
  tasks:
    - name: ensure wireshark is at the latest version
      yum:
        name: wireshark
        state: latest

- name: update LB server
  hosts: lb
  remote_user: ubuntu
  become: yes
  become_user: root
  tasks:
    - name: Update apt repo
      apt: 
        update_cache: yes

    - name: ensure wireshark is at the latest version
      apt:
        name: wireshark
        state: latest
```

Save the file Ctrl + S

## STEP 6 – UPDATE GIT WITH THE LATEST CODE

Now you have a separate branch, you will need to know how to raise a [Pull Request (PR)](https://docs.github.com/en/github/collaborating-with-issues-and-pull-requests/about-pull-requests), get your branch peer reviewed and merged to the master branch.

- Use git commands to add, commit and push your branch to GitHub.

`git status`

`git add .`

`git commit -m "commit message"`

- Create a Pull request (PR) 

- Wear a hat of another developer for a second, and act as a reviewer.

- If the reviewer is happy with your new feature development, merge the code to the master branch.

- Head back on your terminal, checkout from the feature branch into the master, and pull down the latest changes.

- Once your code changes appear in master branch – Jenkins will do its job and save all the files (build artifacts) to `/var/lib/jenkins/jobs/ansible/builds/<build_number>/archive/` directory on `Jenkins-Ansible` server.

## STEP 7 – RUN FIRST ANSIBLE TEST

- Execute `ansible-playbook` command and verify if your playbook actually works:

```bash
ansible-playbook -i inventory/dev.yml playbooks/common.yml
```

![alt text](<IMAGE/TASK11 [6].png>)

![alt text](<IMAGE/TASK11 [7].png>)

You can go to each of the servers and check if wireshark has been installed by running which wireshark or wireshark --version

![alt text](<IMAGE/TASK11 [8].png>)

![alt text](<IMAGE/TASK11 [9].png>)

Your updated with Ansible architecture now looks like this:

![alt text](<IMAGE/Diagram 3.11.png>)

### Congratulations
You have just automated your routine tasks by implementing your first Ansible project!

