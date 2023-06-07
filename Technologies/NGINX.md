---
title: nginx
publish: true
---
# NGINX
NGINX is a high performance and modular server that you can use, for example, as a:

- Web server
- Reverse proxy
- Load balancer

This section describes how to NGINX in these scenarios.

## 2.1. Installing and preparing NGINX

Red Hat uses Application Streams to provide different versions of NGINX. This section describes how to:

- Select a stream and install NGINX
- Open the required ports in the firewall
- Enable and start the `nginx` service

Using the default configuration, NGINX runs as a web server on port `80` and provides content from the `/usr/share/nginx/html/` directory.

**Prerequisites**

- RHEL 8 is installed.
- The host is subscribed to the Red Hat Customer Portal.
- The `firewalld` service is enabled and started.

**Procedure**

1. Display the available NGINX module streams:
    
    # `yum module list nginx`
    Red Hat Enterprise Linux 8 for x86_64 - AppStream (RPMs)
    Name        Stream        Profiles        Summary
    nginx       1.14 [d]      common [d]      nginx webserver
    nginx       1.16          common [d]      nginx webserver
    ...
    
    Hint: [d]efault, [e]nabled, [x]disabled, [i]nstalled
    
2. If you want to install a different stream than the default, select the stream:
    
    # `yum module enable nginx:_stream_version_`
    
3. Install the `nginx` package:
    
    # `yum install nginx`
    
4. Open the ports on which NGINX should provide its service in the firewall. For example, to open the default ports for HTTP (port 80) and HTTPS (port 443) in `firewalld`, enter:
    
    # `firewall-cmd --permanent --add-port={80/tcp,443/tcp}`
    # `firewall-cmd --reload`
    
5. Enable the `nginx` service to start automatically when the system boots:
    
    # `systemctl enable nginx`
    
6. Optionally, start the `nginx` service:
    
    # `systemctl start nginx`
    
    If you do not want to use the default configuration, skip this step, and configure NGINX accordingly before you start the service.
    

**Verification steps**

1. Use the `yum` utility to verify that the `nginx` package is installed:
    
    # `yum list installed nginx`
    Installed Packages
    nginx.x86_64    1:1.14.1-9.module+el8.0.0+4108+af250afe    @rhel-8-for-x86_64-appstream-rpms
    
2. Ensure that the ports on which NGINX should provide its service are opened in the firewalld:
    
    # `firewall-cmd --list-ports`
    80/tcp 443/tcp
    
3. Verify that the `nginx` service is enabled:
    
    # `systemctl is-enabled nginx`
    enabled
    

**ADDITIONAL RESOURCES**

- For details about Subscription Manager, see the [Using and Configuring Subscription Manager](https://access.redhat.com/documentation/en-us/red_hat_subscription_management/1/html/rhsm) guide.
- For further details about Application Streams, modules, and installing packages, see the [Installing, managing, and removing user-space components](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/8/html/installing_managing_and_removing_user-space_components/) guide.
- For details about configuring firewalls, see the [Securing networks](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/8/html/securing_networks/) guide.

## 2.2. Configuring NGINX as a web server that provides different content for different domains

By default, NGINX acts as a web server that provides the same content to clients for all domain names associated with the IP addresses of the server. This procedure explains how to configure NGINX:

- To serve requests to the `example.com` domain with content from the `/var/www/example.com/` directory
- To serve requests to the `example.net` domain with content from the `/var/www/example.net/` directory
- To serve all other requests, for example, to the IP address of the server or to other domains associated with the IP address of the server, with content from the `/usr/share/nginx/html/` directory

**Prerequisites**

- NGINX is installed
- Clients and the web server resolve the `example.com` and `example.net` domain to the IP address of the web server.
    
    Note that you must manually add these entries to your DNS server.
    

**Procedure**

1. Edit the `/etc/nginx/nginx.conf` file:
    
    1. By default, the `/etc/nginx/nginx.conf` file already contains a catch-all configuration. If you have deleted this part from the configuration, re-add the following `server` block to the `http` block in the `/etc/nginx/nginx.conf` file:
        
        server {
            listen       80 default_server;
            listen       [::]:80 default_server;
            server_name  _;
            root         /usr/share/nginx/html;
        }
        
        These settings configure the following:
        
        - The `listen` directive define which IP address and ports the service listens. In this case, NGINX listens on port `80` on both all IPv4 and IPv6 addresses. The `default_server` parameter indicates that NGINX uses this `server` block as the default for requests matching the IP addresses and ports.
        - The `server_name` parameter defines the host names for which this `server` block is responsible. Setting `server_name` to `_` configures NGINX to accept any host name for this `server` block.
        - The `root` directive sets the path to the web content for this `server` block.
        
    2. Append a similar `server` block for the `example.com` domain to the `http` block:
        
        server {
            server_name  example.com;
            root         /var/www/example.com/;
            access_log   /var/log/nginx/example.com/access.log;
            error_log    /var/log/nginx/example.com/error.log;
        }
        
        - The `access_log` directive defines a separate access log file for this domain.
        - The `error_log` directive defines a separate error log file for this domain.
        
    3. Append a similar `server` block for the `example.net` domain to the `http` block:
        
        server {
            server_name  example.net;
            root         /var/www/example.net/;
            access_log   /var/log/nginx/example.net/access.log;
            error_log    /var/log/nginx/example.net/error.log;
        }
        
    
2. Create the root directories for both domains:
    
    # **mkdir -p /var/www/example.com/**
    # **mkdir -p /var/www/example.net/**
    
3. Set the `httpd_sys_content_t` context on both root directories:
    
    # **semanage fcontext -a -t httpd_sys_content_t "/var/www/example.com(/.*)?"**
    # **restorecon -Rv /var/www/example.com/**
    # **semanage fcontext -a -t httpd_sys_content_t "/var/www/example.net(/.\*)?"**
    # **restorecon -Rv /var/www/example.net/**
    
    These commands set the `httpd_sys_content_t` context on the `/var/www/example.com/` and `/var/www/example.net/` directories.
    
    Note that you must install the `policycoreutils-python-utils` package to run the `restorecon` commands.
    
4. Create the log directories for both domains:
    
    # **mkdir /var/log/nginx/example.com/**
    # **mkdir /var/log/nginx/example.net/**
    
5. Restart the `nginx` service:
    
    # **systemctl restart nginx**
    

**Verification steps**

1. Create a different example file in each virtual host’s document root:
    
    # **echo "Content for example.com" > /var/www/example.com/index.html**
    # **echo "Content for example.net" > /var/www/example.net/index.html**
    # **echo "Catch All content" > /usr/share/nginx/html/index.html**
    
2. Use a browser and connect to `http://example.com`. The web server shows the example content from the `/var/www/example.com/index.html` file.
3. Use a browser and connect to `http://example.net`. The web server shows the example content from the `/var/www/example.net/index.html` file.
4. Use a browser and connect to `http://_IP_address_of_the_server_`. The web server shows the example content from the `/usr/share/nginx/html/index.html` file.

## 2.3. Adding TLS encryption to an NGINX web server

This section describes how to enable TLS encryption on an NGINX web server for the `example.com` domain.

**Prerequisites**

- NGINX is installed.
- The private key is stored in the `/etc/pki/tls/private/example.com.key` file.
    
    For details about creating a private key and certificate signing request (CSR), as well as how to request a certificate from a certificate authority (CA), see your CA’s documentation.
    
- The TLS certificate is stored in the `/etc/pki/tls/certs/example.com.crt` file. If you use a different path, adapt the corresponding steps of the procedure.
- The CA certificate has been appended to the TLS certificate file of the server.
- Clients and the web server resolve the host name of the server to the IP address of the web server.
- Port `443` is open in the local firewall.

**Procedure**

1. Edit the `/etc/nginx/nginx.conf` file, and add the following `server` block to the `http` block in the configuration:
    
    server {
        listen              443 ssl;
        server_name         example.com;
        root                /usr/share/nginx/html;
        ssl_certificate     /etc/pki/tls/certs/example.com.crt;
        ssl_certificate_key /etc/pki/tls/private/example.com.key;
    }
    
2. For security reasons, configure that only the `root` user can access the private key file:
    
    # **chown root:root /etc/pki/tls/private/example.com.key**
    # **chmod 600 /etc/pki/tls/private/example.com.key**
    
    Warning
    
    If the private key was accessed by unauthorized users, revoke the certificate, create a new private key, and request a new certificate. Otherwise, the TLS connection is no longer secure.
    
3. Restart the `nginx` service:
    
    # **systemctl restart nginx**
    

**Verification steps**

- Use a browser and connect to `https://example.com`