ElasticPress - Autosuggest
===========================

Extend ElasticPress's search inputs to display search suggestions

## Setup
You need to add an endpoint to your nginx config, that forwards requests for `/es-search` to your Elasticsearch host. Here is an example server block with the required location block:

```
server {
    listen       80;

    server_name  example.com;

    # Elasticsearch endpoint
    location /es-search {

        # only allow things to hit the _autosuggest API
        # change the `_endpoint` to be whatever you'd like to restrict usage to
        location ~* (.*)_suggest$ {
      
            # only allow POST requests
            limit_except POST {
                deny all;
            }
        
            # Perform our request
            rewrite ^/es-search(.*) $1 break;
            proxy_set_header Host $host;

            # Use the URL of the server here
            proxy_pass http://192.168.50.4:9200;
        }

        return 403;
    }

    root         /srv/www/example.com;

    include      /etc/nginx/nginx-wp-common.conf;
}
```
