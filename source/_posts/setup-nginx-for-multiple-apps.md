---
title: Setup Nginx for Multiple Apps
date: 2013-09-17 19:19:32
tags:
---
If anyone wants to set up a few testing sites on only one IP server, we could modify the Nginx config file to open different port for servers deployment.


First, look for the config file

```bash
$ vi nginx/conf/nginx.conf
```

Then, take the below codes for sample.

```bash nginx/conf/nginx.conf
  server {
    listen 180;
    server_name localhost;
    root /home/ruling/orbit_1/public;   # <--- be sure to point to 'public'!
    passenger_enabled on;
    rails_env production;

    location /static/ {
      root /home/ruling/orbit_1/public;
    }

    location ~ ^/(assets)/  {
      root /home/ruling/orbit_1/public;
      expires max;
      add_header Cache-Control public;
    }
  }

  server {
    listen 280;
    server_name localhost;
    root /home/ruling/orbit_2/public;   # <--- be sure to point to 'public'!
    passenger_enabled on;
    rails_env production;

    location /static/ {
      root /home/ruling/orbit_2/public;
    }

    location ~ ^/(assets)/  {
      root /home/ruling/orbit_2/public;
      expires max;
      add_header Cache-Control public;
    }
  }

```


