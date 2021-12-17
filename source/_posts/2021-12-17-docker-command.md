---
title: docker-command
date: 2021-12-17 17:38:25
tags: 
    -- docker
---

<!-- more -->

compose停止运行:

``` shell
docker-compose down
```

重新构建并运行：

``` shell
docker-compose up -d
```

部署portainer-ce

``` shell
docker run -d -p 9000:9000 \
--restart=always \
-v /var/run/docker.sock:/var/run/docker.sock \
--name prtainer-test \
portainer/portainer-ce
```

``` 网心云
docker run -d --name wxedge \
--privileged \
--network=host \
--tmpfs /run \
--tmpfs /tmp \
-v /mnt/Docker/wxy:/storage:rw \
--restart=always \
registry.cn-hangzhou.aliyuncs.com/onething/wxedge
```
