---
title: Tips for Playing around with Rubber Gem for AWS Deployment
abbrlink: 467527d0
date: 2013-12-11 19:47:44
tags:
---
When deploying on Amazon EC2, we have to be careful about the instances we choose. RailsCasts has provided a screencast on how to deploy Rails application on EC2, but the default values are quite a mess.

First of all, default Instance is **m1.medium** for VM Image type, which is not free, so we have to set the YAML file to **t1.micro** according to Amazon Free Tier description.

```yaml
image_type: t1.micro
image_id: 'ami-5e215b0c'
```

Besides, newly created account has only 5 limited **Security Groups** for us to set up, so we have to make sure the Rubber does not generate more than that number.

```yaml
auto_security_groups: false
```

Somehow the multiple intances have trouble connecting to each other, we might want to config the `pg_hba.conf` file to allow IP connection.

```bash
host    all         all         172.XX.XX.XX/24   trust
host    all         all         172.XX.XX.XX/24   trust
```

