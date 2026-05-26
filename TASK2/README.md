### WEB STACK IMPLEMENTATION (LEMP STACK)

## Preparing prerequisites 

- I already have an AWS Account I use, but if you don't please create one as it is necessary for this project.

- Create an EC2 instance in AWS with Ubuntu Server.

- Make sure you also create a KEY PAIR or use the previous one you created for the Task 1, You can leave the rest settings as default

![alt text](<IMAGE/EC2 IMAGE 1.png>)

## STEP 1 - INSTALLING THE NGINX WEB SERVER 

- in order to display web pages to your site visitors, you are going to employ Nginx. You will use the apt package manager to install this package.

- Update your server's package index. Following that, you can `apt` install to get Nginx installed:

```bash
sudo apt update
```
```bash
sudo apt install nginx
```

![alt text](<IMAGE/image 1 [task2].png>)

![alt text](<IMAGE/image 2 [task2].png>)

- To verify that nginx was successfully installed and is running as a service in Ubuntu, run:

```bash
sudo systemctl status nginx
```

![alt text](<IMAGE/image 3 [task3].png>)

- If it is green and running just as it appears in the image above, then you did everything correctly.

- If you encounter an issue just like the image below, do not panic; it means another server is running. Verify which server is running and shut it down. Run these commands:

```bash
sudo systemctl stop <server name>
sudo systemctl disable <server name>
```
![alt text](<IMAGE/image 4 [task2].png>)

- After the server is shut down, run these commands below:

```bash
sudo systemctl restart nginx
sudo systemctl enable nginx
```
- Run the command below to verify that nginx is running:

```bash
sudo systemctl status nginx
```
- Before we can receive any traffic by our Web Server, we need to open TCP port 80 which is default port that web browsers use to access web pages in the Internet. Check out task 1 to see how to enable TCP port 80

- Now it is time for us to test how our Nginx server can respond to requests from the Internet after opening TCP port 80.

- Open a web browser of your choice and try to access following url:

http://Public-IP-Address:80

## STEP 2 - CONFIGURING NGINX TO USE PHP PROCESSOR

- Create the root web directory for your domain as follows:

```bash
sudo mkdir /var/www/projectLEMP
```
- Assign ownership of the directory with the $USER envirnoment variable, which will reference your current system user:

```bash
sudo chown -R $USER:$USER /var/www/projectLEMP
```
- Open a new configuration file in Nginx's `site-available` directory using your preferred command line editor. Here, we will use `nano`:

```bash
sudo nano /etc/nginx/sites-available/projectLEMP
```

- This will create a new blank file. Paste in the following bare-bones configuration:

 
