# linode-cheatsheet
How to setup a webserver on linode

## Domain Setup

1. Buy a domain name from some registrar(godaddy, namecheap, etc.)
1. Add linode nameservers in the registrar's UI
    ```
    ns1.linode.com
    ns2.linode.com
    ns3.linode.com
    ns4.linode.com
    ns5.linode.com
    ```
1. Fill out the "Create Domain" form in linode: https://cloud.linode.com/domains/ and point the domain at one of your nodes

## Reverse Proxy Setup

Install and setup nginx: https://www.linode.com/docs/guides/use-nginx-reverse-proxy/

1. Update software
    ```
    sudo apt update && sudo apt upgrade
    ```
1. Update sources for nginx: `vi /etc/apt/sources.list`
    1. Add the following to the bottom: `deb http://nginx.org/packages/mainline/ubuntu/ noble nginx` noble is for Ubuntu 24, change the codename accordingly if using a newer release
1. Install nginx:
    ```shell
    sudo wget http://nginx.org/keys/nginx_signing.key
    sudo apt-key add nginx_signing.key
    sudo apt install nginx
    sudo systemctl start nginx
    sudo systemctl enable nginx
    ```
1. Configure nginx
    ```
    server {
      listen 80;
      server_name hylandee.com www.hylandee.com;

      # Root for static files
      root /var/www/hylandee/static;
      index index.html;

      # Static assets
      location /static/ {
          alias /var/www/hylandee/static/;
          access_log off;
          expires 30d;
      }

      # Try static files first, then pass to application
      location / {
          try_files $uri $uri/ @app;
      }

        # Application backend
        location @app {
            proxy_pass http://127.0.0.1:3000;

            proxy_http_version 1.1;
            proxy_set_header Upgrade $http_upgrade;
            proxy_set_header Connection "upgrade";

            proxy_set_header Host $host;
            proxy_cache_bypass $http_upgrade;

            proxy_set_header X-Real-IP $remote_addr;
            proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        }
    }
    ```
1. Test the config: `sudo nginx -t` it should return a success message
1. Reload the config: `sudo nginx -s reload`
1. Should should now be able to run a webserver listening on port 3000, visit the http(not https endpoint to test), e.g. http://www.hylandee.com

## SSL Cert Setup
You will want to enable HTTPS for your site

1. Install certbot
    ```shell
    sudo apt install certbot python3-certbot-nginx
    sudo certbot --nginx
    ```
1. Setup firewall
    ```shell
    sudo apt install ufw
    sudo systemctl start ufw && sudo systemctl enable ufw
    sudo ufw allow http
    sudo ufw allow https
    sudo ufw enable
    ```
