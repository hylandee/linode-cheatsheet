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
1. Setup nginx:
    ```shell
    sudo wget http://nginx.org/keys/nginx_signing.key
    sudo apt-key add nginx_signing.key
    sudo apt install nginx
    sudo systemctl start nginx
    sudo systemctl enable nginx
    ```
