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
```

## Default HTTP Port - 80

## Default document root
```bash
/var/www/html
```





Apache is a great web service for use in [[Linux]] Operating Systems. 