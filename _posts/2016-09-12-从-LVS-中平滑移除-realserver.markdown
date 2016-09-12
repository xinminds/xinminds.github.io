---
layout: post
title: "从 LVS 中平滑移除 realserver"
date: 2016-09-12 16:32:00
categories: lvs
---

最近有项目需要下线维护某台 realserver，希望能平滑地下线该台 realserver。

在 LVS 中添加 realserver 是很容易的，只需要直接添加即可，一点都不影响线上业务；但要从 LVS 中移除 realserver，就没这么简单了，因为要移除的 realserver 很可能还保持着部分客户端的 tcp 连接，一旦移除该 realserver，这些连接也就被破坏了。必须想个办法解决！

于是翻阅各种 LVS 文档，得知 LVS 有个叫 */proc/sys/net/ipv4/vs/expire_quiescent_template* 的开关，具体作用是：

    0 - disabled (default)
    not 0 - enabled
  
    When set to a non-zero value, the load balancer will expire
    persistant templates when the destination server is quiescent. This
    may be useful, when a user makes a destination server quiescent by
    setting its weight to 0 and it is desired that subsequent otherwise
    persistant connections are sent to a different destination server.
    By default new persistant connections are allowed to quiescent
    destination servers.
  
    If this feature is enabled, the load balancer will expire the
    persistance template if it is to be used to schedule a
    new connection and the destination server is quiescent.

因此，可以将 *expire_quiescent_template* 的值设成非零，这样“静止的的 realserver(LVS 中权值为 0 的 realserver)” 就不会收到新的连接；然后在等 LVS 的 connection hash tables 中相关的连接超时被移除后，该 realserver 就可以下线维护，而且不影响线上业务，也就是所说的“平滑移除”。
