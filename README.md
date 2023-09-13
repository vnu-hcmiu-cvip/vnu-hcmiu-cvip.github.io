# hcmiu-cvip.github.io

To turn on the server, run
```
cloudflared tunnel run
```

For the first time setup, follow the instruction below:

## How to setup to setup server on local machine

### Setup nginx

1. Update the Ubuntu repository information:
```
sudo apt-get update
```
2. Install the package:
```
sudo apt-get install nginx
```
3. Verify the installation:
```
sudo nginx -v
```
4. Download the key used to sign NGINX packages and the repository, and add it to the apt program’s key ring:
```
sudo wget https://nginx.org/keys/nginx_signing.key
sudo apt-key add nginx_signing.key
```
5. Edit the ***/etc/apt/sources.list*** file
```
sudo gedit /etc/apt/sources.list
```
6. Add these lines ***sources.list*** to name the repositories from which the NGINX Open Source source can be obtained:
```
deb https://nginx.org/packages/mainline/ubuntu/ <CODENAME> nginx
deb-src https://nginx.org/packages/mainline/ubuntu/ <CODENAME> nginx
```
+ CODENAME is the codename of an Ubuntu release. To get codename, run ```lsb_release -c```. 
For example:
```
deb https://nginx.org/packages/mainline/ubuntu/ focal nginx
deb-src https://nginx.org/packages/mainline/ubuntu/ focal nginx
```

7. Install NGINX Open Source:
```
sudo apt-get install nginx
```
8. Start NGINX Open Source:
```
sudo nginx
```
9. ***Stop NGINX***. **Don't** run it right now. Just remind it
```
sudo killall nginx
```
10. Move to nginx config folder: ***/etc/nginx/conf.d***
```
cd /etc/nginx/conf.d
```
11. Create and open file: ***weblab.conf***
```
sudo gedit weblab.conf
```
12. Copy the content below to ***weblab.conf*** and save file.
```
server {
    listen       8080;
    server_name  www.hcmiucvip.com hcmiucvip.com; # Domain name
    location / {
        root   /mnt/data/hcmiu-cvip.github.io; # Path to this repository in the host computer
        index  index.html index.htm;
    }
}
```
13. Restart the **nginx**:
```
sudo killall nginx
sudo nginx
```

### Setup Cloudflared:
#### 1. Download and install cloudflared
```
wget -q https://github.com/cloudflare/cloudflared/releases/latest/download/cloudflared-linux-amd64.deb 
sudo dpkg -i cloudflared-linux-amd64.deb
```
#### 2. Authenticate cloudflared
```bash
cloudflared tunnel login
```
Running this command will:
- Open a browser window and prompt you to log in to your Cloudflare account. After logging in to your account, select your hostname.
- Generate an account certificate, the [cert.pem file](/cloudflare-one/connections/connect-apps/install-and-setup/tunnel-useful-terms/#cert-pem), in the [default `cloudflared` directory](/cloudflare-one/connections/connect-apps/install-and-setup/tunnel-useful-terms/#default-cloudflared-directory)

#### 3. Create a tunnel and give it a name

```bash
cloudflared tunnel create cvip
```

Confirm that the tunnel has been successfully created by running:

```bash
cloudflared tunnel list
```

*** Remeber the UUID*** for the next step

### 4. Create a configuration file

First, create a new termianl and cd to the ***.cloudflared/***:
```
cd .cloudflared/
```
Second, create  a configuration file
```
sudo gedit config.yml
```

Third, paster the config below and change the ***UUID*** to the created file:
```
url: http://localhost:8000
tunnel: <Tunnel-UUID>
credentials-file: /root/.cloudflared/<Tunnel-UUID>.json
```
### 5. Start routing traffic
```
cloudflared tunnel route dns cvip hcmiucvip.com
```

### 6. Run the tunnel. 
```
cloudflared tunnel run
```
