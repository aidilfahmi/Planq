# How to Create Explorer

## NGINX Configuration

### Automatic configuration
```
wget https://raw.githubusercontent.com/ping-pub/explorer/master/ping.conf -O /etc/nginx/sites-enabled/explorer.planq.dnsarz.xyz.conf
sudo sed -i "s|server_name  _|server_name $EXPLORER_DOMAIN|"  /etc/nginx/sites-enabled/explorer.planq.dnsarz.xyz.conf
```
### Manual Configuration
Create explorer file configuration in Nginx configuration folder
```
nano /etc/nginx/sites-enabled/your_explorer_server.conf
```
Replace `your_explorer_server` with your own server, ex.
```
nano /etc/nginx/sites-enabled/explorer.planq.dnsarz.xyz.conf
```
Create this sample configuration
```
server {
    listen       80;
    listen  [::]:80;
    server_name explorer.planq.dnsarz.xyz;

    #access_log  /var/log/nginx/host.access.log  main;

    location / {
        root /usr/share/nginx/html;
        index  index.html index.htm;
        try_files $uri $uri/ /index.html;
    }

    #error_page  404              /404.html;

    # redirect server error pages to the static page /50x.html
    #
    error_page   500 502 503 504  /50x.html;
    location = /50x.html {
        root   /usr/share/nginx/html;
    }

    gzip on;
    gzip_proxied any;
    gzip_static on;
    gzip_min_length 1024;
    gzip_buffers 4 16k;
    gzip_comp_level 2;
    gzip_types text/plain application/javascript application/x-javascript text/css application/xml text/javascript application/x-httpd-php application/vnd.ms-fontobject font/ttf font/opentype font/x-woff image/svg+xml;
    gzip_vary off;
    gzip_disable "MSIE [1-6]\.";
```
replace `server_name` with your own server.<br>
### SSL Configuration
```diff
- I will Update Later
```
Restart your nginx.
```
systemctl restart nginx
```


## Explorer Configuration
### Clone Repository
```
cd $HOME
git clone https://github.com/ping-pub/explorer
```

### Create or edit your config file
```
nano $HOME/explorer/src/chains/mainnet/planq.json
```

Here's my configuration for example

```
{
    "chain_name": "planq",
    "api": ["https://api.planq.dnsarz.xyz"],
    "rpc": ["https://rpc.planq.dnsarz.xyz"],
    "snapshot_provider": "",
    "sdk_version": "0.46.3",
    "coin_type": "60",
    "min_tx_fee": "5000000000000000",
    "addr_prefix": "plq",
    "logo": "/logos/planq.png",
    "keplr_features": ["ibc-transfer", "ibc-go", "eth-address-gen", "eth-key-sign"],
    "assets": [{
        "base": "aplanq",
        "symbol": "planq",
        "exponent": "18",
        "coingecko_id": "",
        "logo": "/logos/planq.png"
    }]
}
```
### Build the Explorer
```
cd $HOME/explorer
yarn && yarn build
```
### Copy web file to Nginx html folder
```
cp -r $HOME/explorer/dist/* /usr/share/nginx/html
nginx -s reload
```


