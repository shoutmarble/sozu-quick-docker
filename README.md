# sozu-quick-docker
sozu reverse proxy quick start

This is an example of sozu using docker-compose.

It is intended to run on a VPS server setup to use A/AAAA wild-card sub-domains.

Also, you should also have the following CRT, KEY, and PEM ACME certificates already
working with your domain.

---
### `docker compose up` and check `http://whoami.example.com` and `http://hello.example.com`
#### automatic redirect to `https://`
---
---
### Add the `sozu` bin manually 
---
`config.toml` replace `example.com` with your domain
```TOML
frontends = [
	{ address = "0.0.0.0:80", hostname = "whoami.example.com" },
	{ address = "0.0.0.0:443", hostname = "whoami.example.com", certificate = "/certs/server.crt", key = "/certs/server.key", certificate_chain = "/certs/server.pem" }
]

# additional options for frontends: sticky_session (boolean)

backends  = [
	{ address = "192.168.168.230:80" }
]
```

---

`docker-compose.yml` make sure to match the names to your ACME certificates
```Yaml
services:
    sozu:
        image: sozu/sozu:123
        build:
            context: .
            dockerfile: ubuntu-sozu-docker
        ports:
          - 80:80
          - 443:443
        networks:
            sozu-network:
                ipv4_address: 192.168.168.100
        volumes:
          - ./config.toml:/etc/sozu/config.toml
          - ./sozu-html-errors/:/sozu-html-errors/
          - ./certificates/example.com.crt:/certs/server.crt:ro
          - ./certificates/example.com.key:/certs/server.key:ro
          - ./certificates/example.com.pem:/certs/server.pem:ro
```
