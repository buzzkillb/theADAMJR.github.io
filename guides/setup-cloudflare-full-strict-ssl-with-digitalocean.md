# Setup Cloudflare Full Strict  SSL with DigitalOcean

## Get Started
This tutorial uses Ubuntu 20.04, using a DigitalOcean droplet. The goal of this is to setup full strict SSL with cloudflare for a static website.

I use FileZilla FTP for pasting in file content due to the DigitalOcean terminal having weird paste issues.

View the full tutorial here, where I setup a Discord bot dashboard with NginX:
https://www.youtube.com/watch?v=Ye1OrYwVN-U

## Cloudflare Setup
Ensure that the **Full (strict)** option is selected for this guide to work.
![Full (strict) SSL](https://i.ibb.co/23Zcz95/full-strict-ssl.png)

Also make sure that **SSL/TLS** -> **Origin Server** -> **Authenticated Origin Pulls** are set to **On**.
![Authenticated Origin Pulls](https://i.ibb.co/dt2Wff5/auth-origin-pulls.png)

## Install NginX
`apt-get install nginx`
NginX is used to serve our website on port 80 and 443.

## Create Cloudflare Certificate File
`ctouch /etc/ssl/certs/cloudflare.crt`
Create the cloudflare certificate file.

Copy the contents of this: https://pastebin.com/dmi6AiFD into `cloudflare.crt`

## Create a Cloudflare Origin Certificate

This will be also be used for secure connections with SSL.

![Create a certificate](https://i.ibb.co/4Jr6Zx1/cloudflare-origin-certificate.png)

### Create the Origin Certificate File
`touch /etc/ssl/certs/cert.pem`
Then paste the contents of the Cloudflare **Origin Certificate** into this file.

### Create the Private Key File
`touch /etc/ssl/private/key.pem`
Then paste the contents of the Cloudflare **Private key** into this file.

### Create the NginX Config File
`touch /etc/nginx/sites-available/default`
Then paste in the contents of this, and tweak to fit the needs of your website. Replace values where necessary.
```nginx
server {
    listen 80;
    listen [::]:80;
    server_name 4pg.xyz www.4pg.xyz; # replace with what you are using
    return 302 https://$server_name$request_uri;
}

server {
        listen 443 ssl;
        listen [::]:443 ssl http2;
        ssl on;

        ssl_certificate /etc/ssl/certs/cert.pem;
        ssl_certificate_key /etc/ssl/private/key.pem;

        root /your/root/website/folder; # replace with the root folder of your static website
        index index.html index.htm;

        location / {
		try_files $uri$args $uri$args/ $uri/ /index.html;
        }
}
```

## Reload NginX
You can *optionally* test that the config does not have syntax errors with `nginx -t`.

Type this to reload NginX and activate the config.
`nginx -s reload`

### Secure!
![Secure!](https://i.ibb.co/ZVTk4wy/secure.png)
