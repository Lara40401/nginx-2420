Step1: Installing the necessary software
1. Connect to your digitalocean droplet
	`ssh -i ~/.ssh/do-key your-user-name@your-droplet-Ip`
2. Update you arch linux system to the newest version using pacman
	`sudo pacman -Syu`
3. Install ngix
    `sudo pacman -S nginx`
    (1) you can check the status of nginx
        `systemclt status nginx.service`
    (2) you can enable the nginx
        `sudo systemctl enable nginx`
4. Install vim- Vim is a highly configurable text editor
    `sudo pacman -S vim`

Step2: Nginx configuration
1. Create a new directory for your project, this directory will store your website documents.The -p option stands for "parent" and it allows you to create a directory hierarchy.
    `sudo mkdir -p /web/html/nginx-2420`
2. Open and edit the Nginx configuration file. After you open a file using "vim", type "i" to the insert mode.
    `sudo vim /etc/nginx/nginx.conf`
3. Inside your http block, add the following block to set up a separate server block for your project. The path "/web/html/nginx-2420/nginx-2420.conf" is the path that your new server block configuration file will be located.
    `# load configs`
    `include /web/html/nginx-2420/nginx-2420.conf;`

Step3:
1. Create a new server block configuration file "nginx-2420.conf" for your new server
    ` sudo vim /web/html/nginx-2420/nginx-2420.conf`
2. Add the following code in your nginx-2420.conf file, you should replace the server_name to your droplet Ip address
    ``` 
    server {
       listen 80;
      server_name 143.198.77.134;
  
       root /web/html/nginx-2420;
       index index.html;
  
       location / {
           index index.html;
       }
   }
    ```
step4:
1. create a new index.html in your /web/html/nginx-2420 directory
    `sudo vim /web/html/nginx-2420/index.html`
2. edit the index.html file, copy and paste your html code into this file
```
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>2420</title>
    <style>
        * {
            color: #db4b4b;
            background: #16161e;
        }
        body {
            display: flex;
            align-items: center;
            justify-content: center;
            height: 100vh;
            margin: 0;
        }
        h1 {
            text-align: center;
            font-family: sans-serif;
        }
    </style>
</head>
<body>
    <h1>All your base are belong to us</h1>
</body>
</html>
```
step5:
1. enable and restart you nginx
    enable: `sudo systemctl enable nginx`
    restart:`sudo systemctl restart nginx`

step6:
1. Open you browser, and type your droplet IP address
2. You should see "All your base are belong to us" with css format
    
