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

- Open your <span style="color:yellow">ansible-config-mgt</span> repo by connecting to your github account using Github Codespace.

- Download Github Codespace from the VSC extention and connect to your github repo from the Remote Explorer.

![alt text](<IMAGE/TASK11 [4].png>)

![alt text](<IMAGE/TASK11 [5].png>)


## STEP 3 – BEGIN ANSIBLE DEVELOPMENT

- In your <span style="color:yellow">ansible-config-mgt</span> GitHub repository, create a new branch that will be used for development of a new feature. You can do this directly from VSC terminal by running:

```bash
git checkout -b feature/ansible-development
```

![alt text](<IMAGE/TASK11 [6].png>)

![alt text](<IMAGE/TASK11 [7].png>)

![alt text](<IMAGE/TASK11 [8].png>)

- Checkout the newly created feature branch to your local machine and start building your code and directory structure

- Create a directory and name it **playbooks** – it will be used to store all your playbook files.

- Create a directory and name it **inventory** – it will be used to keep your hosts organised.

- Within the *playbooks* folder, create your first playbook, and name it `common.yml`

- Within the *inventory* folder, create an inventory file (`.yml`) for each environment (*Development, Staging Testing and Production*) `dev`, `staging`, `uat`, and `prod` respectively.

![alt text](<IMAGE/TASK11 [9].png>)

## STEP 4 – SET UP ANSIBLE INVENTORY





