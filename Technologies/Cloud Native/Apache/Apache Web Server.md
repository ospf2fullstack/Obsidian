# Apache Web Server
```bash
yum install httpd # installs the web server
service httpd start # start the httpd service 
service httpd status # output the status of the httpd service
```

# View Logs
```bash
cat /var/log/httpd/access_log # access log

cat /var/log/httpd/error_log # error log
```

# Config File
```bash
/etc/httpd/conf/httpd.conf
> # add as many ofthese as you need to redirect domain to hosts
> 	# <VirtualHost *:80>
> 		# ServerName www.domain.com
> 		# DocumentRoot /var/www/houses
> 	# </VirtualHost>
```

## Default HTTP Port - 80

## Default document root
```bash
/var/www/html
```

## DNS Entry
```bash
/etc/hosts
```

# Troubleshooting
```bash
# always restart after config changes
service httpd restart 
```

Apache is a great web service for use in [[Linux]] Operating Systems. 