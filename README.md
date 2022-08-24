# NGINX Snippets
A collection of NGINX snippets designed to be used for hosting multiple Node.js servers.

## Configuration files

### nginx.conf `/etc/nginx/nginx.conf`
Base server settings that:
* Set-up logging.
* Import other config files.
* Specifies TLS settings.
* Provides variables for other configuration files.

### headers.conf `/etc/nginx/conf.d/headers.conf`
Adds general security headers to all requests.

### ssl.conf `/etc/nginx/sites-enabled/ssl.conf`
Force all HTTP connections to retry via HTTPS.  Subsequently, all other NGINX server blocks should listen on port 443.

### timeout.conf `/etc/nginx/conf.d/timeout.conf`
Configures various request and response timeouts to be 30 seconds.

### proxy-params.conf `/etc/nginx/snippets/proxy-params.conf`
Various proxy parameters that ensure information is correctly passed to the server to be included in each proxy server location block.

## Example server block usage `/etc/nginx/sites-enabled/example.com`
Route requests to the domain `example.com` to the server running locally on the port `9000`.

```
server {
    listen 443;

    server_name subdomain.example.com example.com;
    
    location / {
        proxy_pass http://127.0.0.1:9000;
        include /etc/nginx/snippets/proxy-params.conf;
    }
}
```
