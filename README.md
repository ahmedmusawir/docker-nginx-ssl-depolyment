# docker-nginx-ssl-depolyment

App deployment on Digital Ocean via Docker or otherwise w/ Nginx Reverse lookup &amp; SSL w/ Certbot (Let's Encrypt)

# Setup Nginx with SSL for Docker-Based Deployment

This guide provides a step-by-step process to set up Nginx as a reverse proxy for a Docker-based application, securing it with SSL using Certbot.

## Prerequisites

- A DigitalOcean droplet (or similar server) with Docker installed.
- A domain and subdomain pointed to the server's IP address.
- Basic knowledge of using the terminal and SSH.

## Step 1

### Install Nginx

```
sudo apt update
sudo apt install nginx
```

### Start and enable Nginx to run on boot:

```
sudo systemctl start nginx
sudo systemctl enable nginx
```

### Checks if Nginx is running and provides detailed status information. Good for after-checking after installation.

```
sudo systemctl status nginx
```

## Step 2

### Configure the Firewall

Allow Nginx traffic through the firewall:

```
sudo ufw allow 'Nginx Full'
sudo ufw reload
```

## Step 3

### Configure Nginx as a Reverse Proxy

Create a new Nginx server block for your subdomain:

```
sudo nano /etc/nginx/sites-available/cyberdash.cyberizewebdevelopment.com
```

Add the following configuration:

```
server {
    listen 80;
    server_name cyberdash.cyberizewebdevelopment.com;

    location / {
        proxy_pass http://localhost:8010;
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection 'upgrade';
        proxy_set_header Host $host;
        proxy_cache_bypass $http_upgrade;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
    }
}
```

### Enable the site by creating a symbolic link to the sites-enabled directory:

```
sudo ln -s /etc/nginx/sites-available/cyberdash.cyberizewebdevelopment.com /etc/nginx/sites-enabled/
```

### Test the Nginx configuration:

```
sudo nginx -t
```

### Reload Nginx to apply the changes:

```
sudo systemctl reload nginx
sudo systemctl status nginx
```

Test the site with just HTTP at port 80
http://cyberdash.cyberizewebdevelopment.com
To make sure that the redirection is working properly before setting up SSL. At this point, you might face an Ubuntu Firewall issue. To resolve that:

## Firewall Configuration

If the site is unreachable, ensure the firewall is configured to allow traffic on ports 80 (HTTP) and 443 (HTTPS):

```
sudo ufw status
sudo ufw allow 'Nginx Full'
sudo ufw reload
```

### Ensure the Nginx configuration is correct and that the site is enabled:

```
sudo nginx -t
sudo systemctl reload nginx
```

## Step 4

### Install Certbot and Obtain an SSL Certificate

- Install Certbot and the Nginx plugin:

```
sudo apt install certbot python3-certbot-nginx
```

### Use Certbot to obtain an SSL certificate and configure Nginx:

```
sudo certbot --nginx -d cyberdash.cyberizewebdevelopment.com
```

## Step 5

### Test the HTTPS Setup

- Open your browser and navigate to:

```
https://cyberdash.cyberizewebdevelopment.com/
```

- Verify that the site is served over HTTPS with a valid SSL certificate.

## Step 6

### Set Up Automatic SSL Renewal (Certbot automatically sets up a cron job to renew the certificate before it expires.). We need to test it with a dry run:

Test the automatic renewal process:

```
sudo certbot renew --dry-run
```
