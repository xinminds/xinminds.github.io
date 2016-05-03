---
layout: post
title: "systemd 开机加载 nf_conntrack 并设置 hashsize"
date: 2016-05-03 14:51:33
categories: systemd
---

## 让 systemd 开机加载内核模块

systemd 可通过服务 `systemd-modules-load.service` 在系统启动初期进行内核模块加载，要加载哪些模块可通过 `/etc/modules-load.d/*.conf` 进行配置。

一般情况下配置文件以模块名字命名，如 virtio-net.conf，其内容为需加载的内核模块的名字(这个只是建议的做法，事实上可以随便命名，而且还可以一个配置文件内写多个内核模块，只需分多行列出即可)。空行或者起始字符为 `#` 和 `;` 的行会被忽略。

因此对于 nf_conntrack 模块，我们要创建配置文件 `/etc/modules-load.d/nf_conntrack.conf` 并且定下内容：

>>> nf_conntrack

## 为 nf_conntrack 模块设置 hashsize

由于 nf_conntrack 模块默认的 hashsize 较小，对于实际业务支撑不足，因此需要调大。对于非 systemd 的机器，是可以直接在 `/etc/modules-load.d/nf_conntrack.conf` 中设置的，配置如下：

>>> nf_conntrack hashsize=1250000

然而，对于使用 systemd 的机器此方法不通，服务 `systemd-modules-load.service` 并不支持直接在 `/etc/modules-load.d/*.conf` 中配置参数。

若要对模块参数进行设置，我们需要使用其他配置文件：`/etc/modprobe.d/*.conf` (此配置的要求与 `/etc/modules-load.d/*.conf` 类似)。

回到 `nf_conntrack` 模块，我们可以在配置文件`/etc/modprobe.d/nf_conntrack.conf` 中写加入这么一行：

>>> options nf_conntrack hashsize=1250000

这时只需重新加载模块(重启或者先卸载再加载)就可以了。
