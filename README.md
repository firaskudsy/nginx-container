# Nginx Docker Compose Setup with HTTPS

This repository contains a Docker Compose setup for running an Nginx server with HTTPS enabled using Certbot for SSL certificate generation.

## Prerequisites
- A server with Docker and Podman Compose installed.
- A domain name pointing to the server's IP address.

## Setup Instructions

1. **Clone the Repository**:
   ```bash
   git clone https://github.com/firaskudsy/nginx-container.git
   cd nginx-container
   ```

2. **Start the Services**:
   ```bash
   podman-compose up -d
   ```

3. **Generate SSL Certificates**:
   Run the following command to generate SSL certificates for your domain:
   ```bash
   podman-compose run certbot certonly --webroot --webroot-path=/var/www/certbot --email your-email@example.com --agree-tos --no-eff-email -d your-domain.com
   ```
   Replace `your-email@example.com` with your email address and `your-domain.com` with your domain name.

4. **Restart Nginx**:
   After generating the certificates, restart the Nginx service:
   ```bash
   podman-compose restart nginx
   ```

## Server Configuration for HTTPS Access

To ensure HTTPS access to your server, make the following changes:

1. **Open Firewall Ports**:
   - Allow traffic on ports 80 (HTTP) and 443 (HTTPS):
     ```bash
     sudo firewall-cmd --add-service=http --permanent
     sudo firewall-cmd --add-service=https --permanent
     sudo firewall-cmd --reload
     ```

2. **Verify DNS Settings**:
   - Ensure your domain name points to the server's public IP address.

3. **Check SELinux (if enabled)**:
   - Allow Nginx to use the necessary ports:
     ```bash
     sudo setsebool -P httpd_can_network_connect 1
     ```

## File Structure
- `docker-compose.yml`: Defines the services for Nginx and Certbot.
- `nginx.conf`: Custom Nginx configuration file.
- `certbot/`: Directory for storing SSL certificates and challenge files.

## Troubleshooting

If your Nginx server is not loading, here are some steps to debug and resolve common issues:

1. **Check if the Containers are Running**:
   - Verify that the Nginx and Certbot containers are running:
     ```bash
     podman ps
     ```
   - If the containers are not running, restart the stack:
     ```bash
     podman-compose up -d
     ```

2. **Inspect Logs**:
   - Check the logs for the Nginx container:
     ```bash
     podman logs nginx-server
     ```
   - Check the logs for the Certbot container:
     ```bash
     podman logs certbot
     ```

3. **Verify Firewall Rules**:
   - Ensure that ports 80 (HTTP) and 443 (HTTPS) are open:
     ```bash
     sudo ufw status
     ```
   - If the ports are not open, allow them:
     ```bash
     sudo ufw allow http
     sudo ufw allow https
     ```

4. **Check DNS Settings**:
   - Verify that your domain name points to the correct server IP address.
   - Use a DNS lookup tool to confirm:
     ```bash
     nslookup your-domain.com
     ```

5. **Validate SSL Certificates**:
   - Ensure that the SSL certificates were generated successfully:
     ```bash
     ls ./certbot/conf/live/your-domain.com/
     ```
   - If the certificates are missing, rerun the Certbot command:
     ```bash
     podman-compose run certbot certonly --webroot --webroot-path=/var/www/certbot --email your-email@example.com --agree-tos --no-eff-email -d your-domain.com
     ```

6. **Check Nginx Configuration**:
   - Validate the Nginx configuration file:
     ```bash
     podman exec nginx-server nginx -t
     ```
   - If there are errors, fix them in `nginx.conf` and restart the Nginx container:
     ```bash
     podman-compose restart nginx
     ```

7. **SELinux (if enabled)**:
   - Ensure Nginx has the necessary permissions:
     ```bash
     sudo setsebool -P httpd_can_network_connect 1
     ```

8. **Rebuild the Stack**:
   - If all else fails, rebuild the stack:
     ```bash
     podman-compose down
     podman-compose up -d
     ```

## License
This project is licensed under the MIT License.
