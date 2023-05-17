# Generic Static Website on Kubernetes with nginx


# The Static
```html
<!doctype html>
<head>
	<title>Generic Static Website</title>
	<meta charset="utf-8"/>
	<meta name="viewport" content="width=device-width, initial-scale=1.0">
	<style></style>
</head>
<body>
	<h1>Hello World</h1>
	<div>
	<p>This is a static page placeholder for a kubernetes deployed nginx pipeline</p>
	</div>
	<div>
	<!-- Add an image tag later... -->
</body>
<footer>
</footer>
```

# Infrastructure 
## Dockerfile
```DOCKERFILE
FROM nginx:stable

EXPOSE 443
EXPOSE 80

# nginx config
COPY path.conf /etc/nginx/conf.d/path.conf

# SSL pem
COPY .pem /etc/nginx/conf.d/.pem

# SSL key
COPY .key /etc/nginx/conf.d/.key

# static content
COPY ./path/to/static/page.html /usr/share/nginx/html/page.html

STOPSIGNAL SIGQUIT

CMD ["nginx", "-g", "daemon off;"]
```

### nginx config
```config
# redirect http requests to httpsserver {  
  listen 80 default_server;  
  listen [::]:80 default_server;  
  server_name _;  
  return 301 [https://$host$request_uri](https://hemanthmgowda.medium.com/$host$request_uri);  
}server {  
  listen       443 ssl;  
  underscores_in_headers on;  
  ssl_certificate      /etc/nginx/conf.d/domain.org.pem;  
  ssl_certificate_key  /etc/nginx/conf.d/domain.org.key;  ssl_session_cache    shared:SSL:10m;  
  ssl_session_timeout  10m;  ssl_protocols  TLSv1.2;  
  ssl_ciphers  HIGH:!aNULL:!MD5;  
  ssl_prefer_server_ciphers   on;  
  client_max_body_size 50M;  access_log /var/log/nginx/access.log;  
  error_log  /var/log/nginx/errors.log;  root /usr/share/nginx/html;  
  index index.html;  location / {  
    try_files $uri $uri/ =404;  
  }  
}
```

### SSL Key
--> How to obtain and inject into pipeline repo, use secret repo for key...

### nginx deployment yaml
```yaml
apiVersion: apps/v1
kind: deployment
metadata:
	name: my_nginx_static_site
spec:
	selector:
		matchLabels:
			run: my_nginx_static_site
	replicas: 1
	template:
		metadata:
			labels:
				run: my_nginx_static_site
		spec:
			containers"
			- image: repo:tag
			  name:my_nginx_static_site
				ports:
				- containerPort: 443
```

### nginx service yaml
```yaml
apiVersion: v1
kind: Service
metadata:
	name: my_nginx_static_site
	labels:
		run: my_nginx_static_site
spec:
	ports:
		- name: http
		  port: 80
		  protocol: TCP
		- name: https
		  port: 443
		  protocol: TCP
		  targetPort: 443
	type: LoadBalancer
	selector:
		run: my_nginx_static_site
```

## Docker build
```bash
docker build -t repo:tag
docker push repo:tag
```



# Pipelines
## kubectl
```bash
export KUBECONFIG=kube_config.yml

kubectl apply -f nginx-deployment.yaml

kubectl apply -f nginx-service.yaml

kubectl get servies
# use the output for the load balancer url address. 
```