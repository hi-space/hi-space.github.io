---
title: carla-simulator Setup
tags: env
---

<!--more-->

# Install

```sh
sudo apt-key adv --keyserver hkp://keyserver.ubuntu.com:80 --recv-keys 1AF1527DE64CB8D9
sudo add-apt-repository "deb [arch=amd64] http://dist.carla.org/carla $(lsb_release -sc) main"
sudo apt-get update
sudo apt-get install carla-simulator
```

# References

- [carla-github](https://github.com/carla-simulator/carla)
- [Debian CARLA on Ubuntu 20.04 error](https://github.com/carla-simulator/carla/issues/3374)