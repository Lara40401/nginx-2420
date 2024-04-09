# configure firewall
1. Log in the your server:
`ssh -i ~/.ssh/do-key your-user-name@your-droplet-Ip`

2. Configure ufw 

(1) install ufw`sudo pacman -S ufw`

(2) enable ufw.service`sudo systemctl enable --now ufw.service`

(3) Allow HTTP traffic `sudo ufw allow http`

(4) Allow SSH traffic `sudo ufw allow SSH`

(5) Allow traffic on port 8080 for the backend:`sudo ufw allow 8080`

(6) check the status of ufw `sudo ufw status verbose`

It should show something like below

```
To                         Action      From
--                         ------      ----
80                         ALLOW IN    Anywhere
22/tcp (SSH)               ALLOW IN    Anywhere
8080                       ALLOW IN    Anywhere
80 (v6)                    ALLOW IN    Anywhere (v6)
22/tcp (SSH (v6))          ALLOW IN    Anywhere (v6)
8080 (v6)                  ALLOW IN    Anywhere (v6)
```

#  Configure the backend
##  place backend binary on your system
1. In your host machine, type ` sftp -i ~/.ssh/do-key arch@143.198.77.134` to log in with sftp
2. To upload your local file(hello-server) to your linux server
`put the/path/to/your/hello-server`
3. Move the file(hello-server) to the logical location
`sudo mv hello-server /usr/local/bin/`
`sudo chmod +x /usr/local/bin/hello-server`
## create a new service file to run the backend
1. create and edit your service file, for example: `sudo vim /etc/systemd/system/hello-server.service`
2. In your service file, it should be have three parts: Unit, Service, Install
```[Unit]
Description=Hello Server
After=network.target

[Service]
Type=simple
ExecStart=/usr/local/bin/hello-server
Restart=always

[Install]
WantedBy=multi-user.target
```
Note: The ExecStart should be the location of your server file
3. To reload systemd manager configuration`sudo systemctl daemon-reload`
4. To start your "hello-server"`sudo systemctl start hello-server`
5. To enable your "hello-server"`sudo systemctl enable hello-server`-- it will create a symbolink

## edit nginx server block, add reverse proxy to backend
`sudo vim /etc/nginx/sites-available/nginx-2420`

```
server {
    listen 80;
    listen [::]:80;
    server_name _;
    root /web/html/nginx/nginx-2420; 
    index index.html;
    location / {
        # First attempt to serve request as file, then
        # as directory, then fall back to displaying a 404.
        try_files $uri $uri/ =404;
    }

    location /hey {
        proxy_pass http://127.0.0.1:8080;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
    }

    location /echo {
        proxy_pass http://127.0.0.1:8080;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
    }
}



```
to test the configuration file for nginx: `sudo nginx -t`
# test connecting to your backend
1. `curl http://you droplet address/hey`-- it will show"Hey there"
2.`curl -X POST -H "Content-Type: application/json" \ -d '{"message": "Hello from your server"}' \ http://your droplet address/echo`--it will show {"message": "Hello from your server"}