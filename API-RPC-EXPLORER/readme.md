# Guide Create RPC API Node Cosmos


## Prepare : 
Make sure you already have your domain and create some sub-domain, here is my domain list:

> Domain : dnsarz.xyz <p>
>Subdomain: <br>
API   : api.planq.dnsarz.xyz <br>
RPC   : rpc.planq.dnsarz.xyz <br>
gRPC  : grpc.planq.dnsarz.xyz <br>
Explorer: explorer.planq.dnsarz.xyz

## Port and Address configuration
### Setting API in app.toml
```bash
nano $HOME/.planqd/config/app.toml
```
  ![image](https://user-images.githubusercontent.com/16186519/215938043-9c33a642-3bc2-4cb5-985d-17de29dd74fa.png)

> 1. Enable API status. <br>
> 2. Save address and port number. ex. tcp://0.0.0.0:`1317`<br>
>

### Check RPC IP:PORT in config.toml

```
 nano $HOME/.planqd/config/config.toml
 ```
![image](https://user-images.githubusercontent.com/16186519/215940344-474374af-a53c-45d0-8f0b-6440dbf74eba.png)
<p>
	
So now we have list Configuration RPC and API like this:
> API : 0.0.0.0:1317 <br>
	RPC : 127.0.0.1:26657 <br> <br>
  all port will be diffrent for each nodes, its depend on your settings
	
## Install Package

```
sudo apt update && sudo apt upgrade -y
```
```
sudo apt install nginx certbot python3-certbot-nginx -y
```
```
curl -fsSL https://deb.nodesource.com/setup_16.x | sudo -E bash -
```
```
sudo apt-get update && apt install -y nodejs git
```
```
curl -sL https://dl.yarnpkg.com/debian/pubkey.gpg | gpg --dearmor | sudo tee /usr/share/keyrings/yarnkey.gpg >/dev/null
```
```
echo "deb [signed-by=/usr/share/keyrings/yarnkey.gpg] https://dl.yarnpkg.com/debian stable main" | sudo tee /etc/apt/sources.list.d/yarn.list
```
```
sudo apt-get update && sudo apt-get install yarn -y
```

## Setting Proxy nginx and Install SSL


## NGINX Configuration
> After create some subdomain, now you have to create nginx configuration for each sub-domain. <br>
All NGINX configuration can be found and create at `/etc/nginx/sites-enabled/` folder

### Create API Config
create new config file for api.planq.dnsarz.xyz sub-domain
```
nano /etc/nginx/sites-enabled/api.planq.dnsarz.xyz.conf
```
copy paste this line
```
server {
    server_name api.planq.dnsarz.xyz;
    listen 80;
    location / {
        add_header Access-Control-Allow-Origin *;
        add_header Access-Control-Max-Age 3600;
        add_header Access-Control-Expose-Headers Content-Length;

	proxy_set_header   X-Real-IP        $remote_addr;
        proxy_set_header   X-Forwarded-For  $proxy_add_x_forwarded_for;
        proxy_set_header   Host             $host;

        proxy_pass http://0.0.0.0:1317;

    }
}
```
`ctrl + X + Y `to save it
### Create RPC Config
create new config file for rpc.planq.dnsarz.xyz sub-domain
```
nano /etc/nginx/sites-enabled/rpc.planq.dnsarz.xyz.conf
```
copy paste this line
```
server {
    server_name rpc.planq.dnsarz.xyz;
    listen 80;
    location / {
        add_header Access-Control-Allow-Origin *;
        add_header Access-Control-Max-Age 3600;
        add_header Access-Control-Expose-Headers Content-Length;

	proxy_set_header   X-Real-IP        $remote_addr;
        proxy_set_header   X-Forwarded-For  $proxy_add_x_forwarded_for;
        proxy_set_header   Host             $host;

        proxy_pass  http://127.0.0.1:26657;

    }
}
```

### Config Test

```bash
nginx -t 
```
should be like this

![image](https://user-images.githubusercontent.com/16186519/215943776-442010ab-9a74-4b2e-a47a-e59868e356ae.png)


### Install Certificate SSL

```bash
sudo certbot --nginx --register-unsafely-without-email
```
![image](https://user-images.githubusercontent.com/16186519/215944270-1019d6d1-cf82-49b3-bf08-cf55cb732b45.png)

> Select 1 and press enter <br>
  if the BOT asking for 'redirect', select YES.<br>
  Do the option for all your sub-domains.

<p>

If BOT doesn't asking for redirect your sub-domain, you can use this command:
```
  sudo certbot --nginx --redirect
```

After all done, you can restart NGINX 
```
systemctl restart nginx
```
  
## Checking your API and RPC
Open your browser and check your API sub-domain
> https://api.planq.dnsarz.xyz
  
![image](https://user-images.githubusercontent.com/16186519/215946801-17929a44-0f03-40bc-b80f-a996bf66e235.png)

Open your browser and check your RPC sub-domain
> https://rpc.planq.dnsarz.xyz

![image](https://user-images.githubusercontent.com/16186519/215947098-0c58247b-d879-421c-90ce-172acdaa687d.png)

  
IF everything showing on your browser like that images, then well done, you already create your own API and RPC.
  
Thank you.
