# Create Snapshoot Tutorial

There is no need to create sub-domain. You can use your domain default configuration

This is my configuration:
```bash
My domain : dnsarz.xyz
Main website is : http://dnsarz.xyz
Default web folder (NGINX) : /var/www/html
Default Nginx Configuration : /etc/nginx/sites-enabled/default
```

## Create Directory for snapshot

```bash
mkdir -p /var/www/html/snapshot/planq/
```
## Installation Package

```bash
sudo apt install lz4
cd $HOME/.planqd
sudo systemctl stop planqd
tar -cf - data | lz4 > /var/www/html/snapshot/planq/planq-snapshot-$(date +%Y%m%d).tar.lz4
sudo systemctl start planqd
```

edit `/etc/nginx/sites-enabled/default` and add this line
![image](https://user-images.githubusercontent.com/16186519/215653837-795b5ea2-467b-476f-b231-5b7d8178b5c4.png)

```bash
        location /snapshot/planq {
                # First attempt to serve request as file, then
                # as directory, then fall back to displaying a 404.
                autoindex on;
                autoindex_exact_size off;
                autoindex_format html;
                autoindex_localtime on;
        }
```


## Restart Web Server

```bash
service nginx restart

```

## Check your snapshot
```
http://your_domain/snapshot/planq/ 
```
![image](https://user-images.githubusercontent.com/16186519/215654319-5c840516-6b64-4f55-aec3-55be8afa21e5.png)

# Create cronjob for daily snapshot

### Installation and Configure Crontab
```
sudo apt-get install cron
sudo systemctl enable cron
sudo systemctl start cron
```

### Creating .sh file
My snapshot folder at `/var/www/html/snapshot/planq/`, you must replace with your own working folder to save planq snapshot
```
rm $HOME/cron.sh
sudo tee $HOME/cron.sh > /dev/null << 'EOF'
sudo systemctl stop planqd
cd $HOME/.planqd/
rm /var/www/html/snapshot/planq/*
tar -cf - data | lz4 > /var/www/html/snapshot/planq/planq-snapshot-$(date +%Y%m%d).tar.lz4
sudo systemctl start planqd
EOF
chmod +x $HOME/cron.sh
```

### Creating Daily Cronjob
```
crontab -l > cronjob
echo "0 0 * * * /root/cron.sh" >> cronjob
crontab cronjob
rm cronjob
```
### Check your crontab list
```
crontab -l
```
should be like this

![image](https://user-images.githubusercontent.com/16186519/215921974-009e6d20-0c45-41e9-a6a1-b0282a436b18.png)


