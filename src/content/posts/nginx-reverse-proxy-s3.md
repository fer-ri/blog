---
title: Nginx Reverse Proxy of S3
description: Utilize NGINX reverse proxy to alias S3 object storage URLs seamlessly.
publishDate: 2024-01-09
tags: [nginx, ubuntu, s3]
---

My client possesses a 3D model exported from https://www.soft8soft.com/verge3d/, constituting a static site hosted in Linode's S3 object storage. The challenge arises in seamlessly integrating this site under the same domain as the main website, with concerns regarding CORS and the relative path for the `v3d.js` library.

To address this, our solution involves serving all files from the S3 storage under the main domain using an NGINX reverse proxy. The desired transformation is from the original S3 URL:

```bash
http://mybucket.ap-south-1.linodeobjects.com/3d/index.html
```

to the proxied URL:

```bash
https://domain.com/assets/3d/index.html
```

Here's the corresponding NGINX configuration:

```nginx
server {
    # ... the rest of config

    # This line sets the variable $bucket to the specified S3 storage bucket name.
    set $bucket "mybucket.ap-south-1.linodeobjects.com";

    # Begins a location block that matches requests with the prefix "/assets/".
    # Adjust to your PATH
    location /assets/ {
        # Rewrites the URI to remove the "/assets/" prefix before passing it to the proxy.
        rewrite ^/assets/(.*) /$1 break;

        # Specifies the DNS resolver to use when resolving the domain name in the proxy_pass directive.
        # If your network supported IPV6, then you can omit "ipv6=off".
        resolver 1.1.1.1 ipv6=off;

        # Specifies the HTTP version to use in communication with the proxied server.
        proxy_http_version 1.1;

        # Passes the request to the specified S3 storage bucket using the variable set earlier.
        proxy_pass http://$bucket;

        # These lines set various HTTP headers to be sent to the proxied server.
        # Sets the "Host" header to the value of the $bucket variable.
        proxy_set_header Host $bucket;

        # Sets the "X-Real-IP" header to the client's real IP address.
        proxy_set_header X-Real-IP $remote_addr;

        # Appends the client's IP address to the "X-Forwarded-For" header.
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;

        #
        # BELOW IS OPTIONAL
        #

        # Sets the "Connection" header to an empty string.
        proxy_set_header Connection "";

        # Sets the "Authorization" header to an empty string.
        proxy_set_header Authorization "";

        # These lines hide or ignore specific headers from the response of the proxied server.
        # Adjust to your needs
        proxy_hide_header x-amz-request-id;
        proxy_hide_header x-amz-meta-s3cmd-attrs;
        proxy_hide_header x-amz-storage-class;
        proxy_hide_header x-rgw-object-type;
        proxy_hide_header Set-Cookie; # Usefull to make sure Cloudflare can cached it
        proxy_ignore_headers Set-Cookie; # Usefull to make sure Cloudflare can cached it

        # Adds a custom header "X-Proxy-Pass" with the value "HIT" as info the request was handled by this proxy_pass
        add_header X-Proxy-Pass "HIT";

        # Adds a "Cache-Control" header to the response, indicating a maximum age for caching (1 year).
        add_header Cache-Control max-age=31536000;
    }
}
```

Furthermore, for additional optimization, consider implementing FastCGI Cache or utilizing Cloudflare's caching mechanisms. These steps can significantly enhance the performance and responsiveness of your integrated solution.
