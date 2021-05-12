---
title: Hello Hexo
---
Welcome to [Hexo](https://hexo.io/)! This is your very first post. Check [documentation](https://hexo.io/docs/) for more info. If you get any problems when using Hexo, you can find the answer in [troubleshooting](https://hexo.io/docs/troubleshooting.html) or you can ask me on [GitHub](https://github.com/hexojs/hexo/issues).

## Hexo避坑指南

- https://liguanghe.github.io/2017/11/06/blogRebuilt/

### hexo on WSL
```
在使用WSL安装hexo时遇到nodejs版本问题，默认Ubuntu 20.04 LTS 使用nodejs10，在hexo deploy时会报错：
TypeError: line.matchAll is not a function
我更新到14解决，Google上有说更新到11即可
```

## Origin Quick Start

### Create a new post

``` bash
$ hexo new "My New Post"
```

More info: [Writing](https://hexo.io/docs/writing.html)

### Run server

``` bash
$ hexo server
```

More info: [Server](https://hexo.io/docs/server.html)

### Generate static files

``` bash
$ hexo generate
```

More info: [Generating](https://hexo.io/docs/generating.html)

### Deploy to remote sites

``` bash
$ hexo deploy
```

More info: [Deployment](https://hexo.io/docs/one-command-deployment.html)
