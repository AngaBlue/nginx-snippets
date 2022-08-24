# NGINX Snippets
A collection of NGINX snippets designed to be used for hosting multiple Node.js servers.

I use these settings behind Cloudflare to provide Full (strict) end-to-end encryption.  Using a Cloudflare Origin CA certificate is the easiest way to accomplish this, although this certificate is not trusted by clients; meaning you must use a different certificate issued by CA (such as [Let's Encrypt](https://letsencrypt.org/)) in order to server traffic that does not pass through Cloudflare.

For other NGINX setups, [Digital Ocean's NGINXConfig tool](https://www.digitalocean.com/community/tools/nginx) is a great place to start.

## Configuration files

### nginx.conf `/etc/nginx/nginx.conf`
Base server settings that:
* Set-up logging.
* Import other config files.
* Specifies TLS settings.
* Provides variables for other configuration files.

### headers.conf `/etc/nginx/conf.d/headers.conf`
Adds general security headers to all requests.
* Restrictive CORS & permissions policy
* iFrame blocking
* Tracking blocking

___Note:__ these headers include HSTS, meaning if you don't plan to support HTTPS on all hosted sites now and into the future, you must remove this header._

### ssl.conf `/etc/nginx/sites-enabled/ssl.conf`
Force all HTTP connections to retry via HTTPS.  Subsequently, all other NGINX server blocks should listen on port 443.

By default, a certificate at `/etc/ssl/certs/certificate.pem` with a key at `/etc/ssl/private/certificate.key` are used.  These names and locations can of course be changed.

If multiple certificates are needed for various domains, the default certificate can be overridden inside a server block using the following snippet.

```
ssl_certificate /etc/ssl/certs/other-cert.pem;
ssl_certificate_key /etc/ssl/private/other-cert.key;
```

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
