n8n Deployment with Docker, Nginx, and SSL

This repository contains configuration files to deploy an n8n workflow automation server using Docker, with Nginx as a reverse proxy and Certbot for automatic SSL certificate management via Let's Encrypt.

Overview

The setup includes:





n8n: A workflow automation tool running in a Docker container.



Nginx: A reverse proxy to handle HTTP/HTTPS traffic and serve SSL certificates.



Certbot: Automates SSL certificate issuance and renewal for secure HTTPS connections.

The configuration is defined in two main files:





docker-compose.yml: Defines the Docker services, networks, and volumes.



nginx.conf: Configures Nginx to handle HTTP/HTTPS traffic and proxy requests to n8n.

Prerequisites





Docker and Docker Compose installed on your server.



A domain name (e.g., n8n.yourdomain) pointed to your server's public IP.



Ports 80 and 443 open on your server for HTTP and HTTPS traffic.

File Structure





docker-compose.yml: Configures the n8n, Nginx, and Certbot services, including networking and volume mounts.



nginx.conf: Nginx configuration for redirecting HTTP to HTTPS, serving Let's Encrypt challenges, and proxying requests to n8n.

Setup Instructions





Clone the Repository

git clone <repository-url>
cd <repository-directory>



Update Domain Name





Replace n8n.yourdomainname.com in both docker-compose.yml and nginx.conf with your actual domain name.



Start the ServicesRun the following command to start all services:

docker-compose up -d



Initial Certbot CertificateManually run Certbot to obtain the initial SSL certificate:

docker-compose run --rm certbot certonly --webroot --webroot-path=/var/www/certbot -d n8n.yourdomainname.com


Verify Setup



Access n8n at https://n8n.yourdomainname.com



Ensure HTTP requests redirect to HTTPS.



Verify that n8n is accessible and workflows can be created.

Configuration Details

docker-compose.yml





n8n Service:





Uses the n8nio/n8n:latest image.



Exposes port 5678 for internal communication with Nginx.



Persists data in the n8n_data volume.



Configures environment variables for n8n, including the domain, protocol, and session settings.



Mounts Let's Encrypt certificates for SSL.



Nginx Service:





Uses the nginx:latest image.



Exposes ports 80 and 443 for HTTP and HTTPS traffic.



Mounts the nginx.conf file and Let's Encrypt certificates.



Proxies requests to the n8n service.



Certbot Service:





Uses the certbot/certbot:latest image.



Automatically renews SSL certificates every 12 hours.



Shares volumes with Nginx for certificate access.



Volumes and Networks:





n8n_data: Persistent storage for n8n configuration and workflows.



n8n-network: A bridge network for communication between services.

nginx.conf





Configures Nginx to:





Redirect all HTTP traffic to HTTPS.



Serve Let's Encrypt ACME challenges for certificate issuance/renewal.



Proxy HTTPS requests to the n8n service on port 5678.



Set headers for WebSocket support and client IP forwarding.

Maintenance





Certificate Renewal: Certbot automatically checks for certificate renewal every 12 hours. No manual intervention is required.



Logs:





Nginx logs: /var/log/nginx/error.log (inside the Nginx container).



n8n logs: Use docker logs n8n to view.



Certbot logs: Use docker logs certbot to view.



Updates:





To update n8n, Nginx, or Certbot, pull the latest images and recreate the containers:

docker-compose pull
docker-compose up -d

Troubleshooting





SSL Errors: Ensure the domain is correctly pointed to your server's IP and that ports 80/443 are open.



n8n Not Accessible: Check container logs (docker logs n8n) and verify the N8N_HOST and WEBHOOK_URL environment variables.



Certificate Issues: Manually run the Certbot command above to debug certificate issuance.

License

This project is licensed under the MIT License. See the LICENSE file for details.
