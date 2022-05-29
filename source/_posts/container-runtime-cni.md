---
title: Container Runtime 如何用CNI
categories: cloud native
tags: cloud native
abbrlink: f378b788
date: 2022-05-29 13:08:48
---
## CNI 介绍
CNI是Container Networking Interface的缩写，它的目的是标准化容器运行时引擎和网络实现之间的接口，它是将容器连接到网络的最低标准方法。

[CNI](https://github.com/containernetworking) 项目主要是做了三件事情：

1. [CNI接口的定义](https://github.com/containernetworking/cni/blob/master/SPEC.md)
2. [Golang的library](https://github.com/containernetworking/cni/blob/1773a1f24c559506d7d36e838db19338e266878f/cnitool/cnitool.go#L25) 提供CNI接口的实现。
3. [CNI插件](https://github.com/containernetworking/plugins)的实现，包括Bridge,ipvlan,macvlan等。

可以用下面这张图来总结CNI的工作原理：

![image](https://imgconvert.csdnimg.cn/aHR0cHM6Ly9ub3RlLnlvdWRhby5jb20veXdzL3B1YmxpYy9yZXNvdXJjZS8yNGQ5ODVkYjllMjA0MzI0MDUxMGMzMTg2YTcxNTdkZS94bWxub3RlLzU3NUI2NkE3RkU4NTQ1Mjc5NkU3QjBCM0E0QzMyMEE0LzEwODQ3)

在理解CNI时，需要注意以下几点：
- 实现CNI标准的插件是二进制的，而不是守护进程。在运行时，它应该至少具有[cap-net-admin](http://man7.org/linux/man-pages/man7/capabilities.7.html)功能。
- 网络定义或网络配置存储为JSON文件。这些JSON文件通过stdin流式传输到插件。类似的json文件在k8s中会存储在/et/cni/net.d/下面，如：10-calico.conflist
- 只有在创建容器（运行时变量）时才知道的任何信息都应该通过环境变量传递给插件。不过，在最新的CNI中，也可以通过stdin上的JSON发送某些运行时配置，特别是对于一些扩展和可选功能, [参考](https://github.com/containernetworking/cni/blob/master/CONVENTIONS.md)
- 二进制文件中不应该有上述两个之外的任何其他输入配置。
- CNI插件负责连接容器，并希望隐藏网络复杂性。

## 容器运行时(Container Runtime)介绍
容器运行时engine是一个守护进程，位于容器调度和容器创建的二进制文件的实际实现之间。这个守护进程不一定需要作为根用户运行，它监听来自调度程序的请求。它通过容器标准(OCI)，使用外部二进制文件来实际创建或删除容器。

例如，在kubernetes中，容器运行时可以是cri-o或cri-containerd），它监听来自kubelet的请求，kubelet是通过cri接口从位于每个节点的调度程序发出的代理，容器运行时通过OCI标准方式，包括OCI-Image和OCI-Runtime，调用runc（实现OCI运行时规范的二进制文件，或者如：kata-runtime）来创建容器，调用flannel（实现CNI的二进制文件，或者如：calico等）来配置网络。上述过程，如下图：

![image](https://imgconvert.csdnimg.cn/aHR0cHM6Ly9ub3RlLnlvdWRhby5jb20veXdzL3B1YmxpYy9yZXNvdXJjZS8yNGQ5ODVkYjllMjA0MzI0MDUxMGMzMTg2YTcxNTdkZS94bWxub3RlLzE4M0ZGQzRDMjlERTQ2RkY4MzlGOEE3QTg0RjlDRDA0LzEwODg1)

容器运行时需要执行以下操作才能真正创建可用的容器：
- 创建rootfs文件系统。
- 创建容器（在命名空间中独立运行并受cgroups限制的进程集）。
- 将容器连接到网络。
- 启动用户进程。

如下图：
![image](https://imgconvert.csdnimg.cn/aHR0cHM6Ly9ub3RlLnlvdWRhby5jb20veXdzL3B1YmxpYy9yZXNvdXJjZS8yNGQ5ODVkYjllMjA0MzI0MDUxMGMzMTg2YTcxNTdkZS94bWxub3RlLzM4MzQ2N0EyNTQyRjRDQUU5OUMzMzg4NDJFNkIxQTQ2LzEwODkz)

就网络部分而言，最重要的是容器运行时要求OCI运行时二进制文件将容器进程放入新的网络命名空间（Net namespace）。然后容器运行时将使用新的网络名称空间作为运行时环境变量变量调用CNI插件。CNI插件应该拥有所有的信息，以便实现网络配置。

## 容器运行如何使用CNI
下面以一个例子，说明容器运行时如何使用CNI的[Bridge](https://github.com/containernetworking/plugins/tree/master/plugins/main/bridge)
将容器连接到网桥，下面将使用简单的bash命令"模拟"运行时的操作。
### 配置阶段
在完成下面的例子演示之前，需要做基本的服务器配置。只需要确保所需的二进制程序存在即可。需要OCI运行时二进制(Runc), CNI插件二进制(Bridge，Host Loca(用于ip的分配))。
我们可以从github下载预构建的二进制文件，也可以从源代码构建二进制文件。前提是需要一个go的环境。

```
go get github.com/opencontainers/runc
go get github.com/containernetworking/plugins
cd $GOPATH/src/github.com/containernetworking/plugins
./build.sh
sudo mkdir -p /opt/cni/{bin,netconfs}
sudo cp bin/* /opt/cni/bin/
which /opt/cni/bin/{bridge,host-local} runc
```
在配置阶段，先创建容器需要连接的Bridge, 类似docker0的网桥

```
ip link add name br0 type bridge
ip addr add 10.10.10.1/24 dev br0
ip link set dev br0 up
```
然后，添加网络配置的文件如下：

```
export NETCONFPATH=/opt/cni/netconfs
cat > $NETCONFPATH/10-mynet.conf <<EOF
{
    "cniVersion": "0.2.0",
    "name": "mynet",
    "type": "bridge",
    "bridge": "br0",             
    "isGateway": true,
    "ipMasq": true,
    "ipam": {
        "type": "host-local",
        "subnet": "10.10.10.0/24",
        "routes": [
            { "dst": "0.0.0.0/0" }
        ],
     "dataDir": "/run/ipam-state"
    },
    "dns": {
      "nameservers": [ "8.8.8.8" ]
    }
}
EOF
```
### 容器Runtime阶段
容器调度器(如：k8s)最终将命令容器运行时启动容器。运行时将执行以下简化步骤：

```
# Step 1: 创建 the rootfs 目录
mkdir bundle && cd bundle/
mkdir -p rootfs && docker export $(docker create busybox) | tar -C rootfs -xvf -

# Step 2: 创建 OCI runtime config
runc spec
# Step 3: 启动container
runc run busyboxid
# Step 4: 另一个窗口，找到net namespace的路径，并软连接到/var/run/netns/xx
ns=$(cat /var/run/runc/busyboxid/state.json | jq '.namespace_paths.NEWNET' -r)
mkdir -p /var/run/netns
ln -sf $ns /var/run/netns/busyboxid
ip netns 

# $ runc list
# ID          PID         STATUS      BUNDLE         CREATED                          OWNER
# busyboxid   17136       running     /root/bundle   2019-08-04T14:15:11.927965079Z   root
```
然后再创建bash环境变量，包括了有网络namespace的容器运行时所需要的信息。

```
export NETCONFPATH=/opt/cni/netconfs
export CNI_PATH=/opt/cni/bin/
export CNI_CONTAINERID=busyboxid
export CNI_NETNS=/var/run/netns/busyboxid
export CNI_IFNAME=eth0
export CNI_COMMAND=ADD
```
最后，将调用在stdin中提供可配的conf和上述变量的cni二进制文件。运行时将以JSON格式返回结果。

```
$ cat $NETCONFPATH/10-mynet.conf | $CNI_PATH/bridge
{
    "cniVersion": "0.2.0",
    "ip4": {
        "ip": "10.10.10.2/24",
        "gateway": "10.10.10.1",
        "routes": [
            {
                "dst": "0.0.0.0/0",
                "gw": "10.10.10.1"
            }
        ]
    },
    "dns": {
        "nameservers": [
            "8.8.8.8"
        ]
    }
}

```
最后，可以通过在容器网络空间内运行IP命令来检查网络接口是否已正确设置。

```
$ ip netns exec busyboxid ip a s eth0
3: eth0@if5: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc noqueue state UP group default
    link/ether d6:d6:48:92:b3:25 brd ff:ff:ff:ff:ff:ff link-netnsid 0
    inet 10.10.10.2/24 scope global eth0
       valid_lft forever preferred_lft forever
    inet6 fe80::d4d6:48ff:fe92:b325/64 scope link
       valid_lft forever preferred_lft forever
$ ip netns exec busyboxid ip route
default via 10.10.10.1 dev eth0
10.10.10.0/24 dev eth0  proto kernel  scope link  src 10.10.10.2
```

删除，只需更改CNI_COMMAND即可：

```
export CNI_COMMAND=DEL
cat $NETCONFPATH/10-mynet.conf | $CNI_PATH/bridge
# no output expected when success
```
## Multi-Interface 情况
如果需要将容器连接到多个网络，也就是说在容器中可以配置多个网卡，可以通过$netconfpath中的多个网络配置实现。参考如下的shell:

```
export CNI_COMMAND=ADD
for conf in $NETCONFPATH/*.conf; do
  echo "${CNI_COMMAND}ing $conf"
  export CNI_IFNAME=$(cat $conf | jq -r '.name')
  plugin=$(cat $conf |jq -r '.type')
  echo "cat $conf | $CNI_PATH/$plugin"
  res=$(cat $conf | $CNI_PATH/$plugin)
  echo $res | jq -r .
done
```
基本上是对每个网络配置进行循环添加。

## 总结
CNI负责了在容器创建或删除期间的所有与网络相关的操作，它将创建所有规则以确保从容器进和出的网络连接正常，但它并不负责设置网络介质，例如创建网桥或分发路由以连接位于不同主机中的容器。CNI的目标是隐藏网络复杂性，以使运行时代码库更干净，同时使第三方提供商能够创建自己的插件，并将它们轻松集成到使用CNI标准的的所有容器编排器中。

## 参考
1. http://www.dasblinkenlichten.com/understanding-cni-container-networking-interface/
2. https://jvns.ca/blog/2016/12/22/container-networking/

