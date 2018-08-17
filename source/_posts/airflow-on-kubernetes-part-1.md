---
title: Airflow on Kubernetes Part I
date: 2018-08-17 08:47:24
tags: airflow
keywords: kubernetes,k8s,airflow,devops
description:
---

Building an industrial design on Kubernetes is hard, but building on top of pre-existed application without support is even harder.

I found two repos are quite suitable for such cases

[Docker Airflow](https://github.com/mumoshu/kube-airflow)

[Kube Airflow](https://github.com/mumoshu/kube-airflow)

Due to the fact that Kube Airflow expects Helm chart supports for K8S in mind and less actively maintain, and later I found [https://github.com/helm/charts/issues/2591](https://github.com/helm/charts/issues/2591) has already started an official support for the Airflow. Therfore, I decided to use simplified Docker Airflow image for deployment for now if Airflow chart is still at its early stage.

Even so, a simple Docker Airflow could cause you nightmares too. That's why I guessed many people have forked their own versions. The Python dependencies are not specifically locked into the `Dockerfile`, so basically when building an image will install the latest version first which might crash Airflow application. I suggest to lock the specific version to make sure every developers will consume the same environments on their own machines. Of course, that's what Docker meant for, isn't it?

Since we're deploying on Kubernetes which is already a great support for microservices on `Pods` and `Containers` distributions, keeping `LocalExecutioner` for `docker-compose` won't reflect too much on our real deployment. Removing the `LocalExecutioner` for `docker-compose` is fine and make sure the `CeleryExecutioner` should work well for the local  development. However, I suggest to have environment variables put into files like `.env` for better management of keys instead of hardcoded. Later on, we could `.gitignore` these files and deploy more freely for  different environments setting on machines.
