# postman_toxiproxy
Postman Collections for toxiproxy to simulate a latency toxic.

# To get toxiproxy running

```
docker run --rm --net=host --name=toxiproxy -it shopify/toxiproxy
```

Then you can address toxiproxy container by accessing through `localhost:8474` (default in postman collection).

# Rest

Start postman

Let's say you want to add a latency of 1000 ms with a likelihood of 50% (equals 0.5) to the connection to *google.com* on your local machine.

Configure the following variables in the postman collection:

| Name | Value |
|------|-------|
| downstream_host | google.com |
| downstream_port | 80 |
| upstream_port | 80 |
| toxi_latency  | 1000 |
| toxicity | 0.50 |
| toxi_name | wait_a_second |
| proxy_name | google |

Now execute the two POST requests from the postman collection:
* POST /proxies
* POST /proxies/{{proxy_name}}/toxics

This will yield a GET /proxies as follows
```
curl -X GET \
  http://127.0.0.1:8474/proxies \
  -H 'Accept: */*' \
  -H 'Cache-Control: no-cache' \
  -H 'Connection: keep-alive' \
  -H 'Host: 127.0.0.1:8474' \
  -H 'Postman-Token: 53ca8601-b439-4452-a453-146286c72efd,3487c0ec-c936-4d13-bd6c-28c552c91236' \
  -H 'User-Agent: PostmanRuntime/7.15.0' \
  -H 'accept-encoding: gzip, deflate' \
  -H 'cache-control: no-cache'
```

```
{
    "google": {
        "name": "google",
        "listen": "127.0.0.1:80",
        "upstream": "google.com:80",
        "enabled": true,
        "toxics": [
            {
                "attributes": {
                    "latency": 1000,
                    "jitter": 0
                },
                "name": "wait_a_second",
                "type": "latency",
                "stream": "upstream",
                "toxicity": 1
            }
        ]
    }
}
```

Now add to your */etc/hosts*
```
127.0.0.1 localhost mygoogle.com
```

The call ```wget -O- http://mygoogle.com``` should have a delay of +1s.
