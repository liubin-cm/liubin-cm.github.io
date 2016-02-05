title: kubernetes trouble shooting
tags: kubernetes debug
date: 2016-02-04 17:07:40
---
<!-- toc -->
## 查看logs
1. of pod:
	* kubectl log **&lt;podname&gt;** [**&lt;containername&gt;**]
2. Of kubernetes system components:
<!-- more -->
	* 依赖于操作系统，系统组件以及docker的log位于/var/log或者/tmp目录下.
    * 或者通过journalctl来访问相关日志，例如sudo journalctl -r -u kubelet
    * salt minion上的logs位于/var/log/salt/minion目录下。
    * 如果在如上日志中没有获得由价值的信息，使用--v 或 --vmodule打开你怀疑有问题的kubernetes组件的详细日志开关，开关级别不低于4级。详细请查看https://github.com/golang/glog。
    * 可以使用docker ps -a查看创建了哪些容器。
    * 使用kubectl get events查看kubernetes正在发生什么事情
    * 到/var/log/messages查看系统日志
    
## 查看etcd内容  
* 使用[etcdctl](https://github.com/coreos/etcd/tree/master/etcdctl)查看etcd内容。

## 确保基础服务运行正常  
1. 保证所有的后台内容都已经运行起来。
	* 在master上，确保apiserver, controller, scheduler, etcd都已经运行起来
	* 在node上，确保proxy, kubelet, docker已经正常运行  
2. 确保所有的k8s组件都指定了正确的--api_servers

## 常见问题
* Pods stay Pending
	* 使用kubectl describe pod podname查看pod最后发生了什么事情。
	* 使用kubectl get events查看kubernetes发生的事件
	* 确认kubelet与api_servers是否建立联系，‘Host’与node节点上的hostname()是否一致；Host与--hostname_override指定的kubelet参数是否一致。
* kubectl无法与apiserver建立连接
	* 检查KUBERNETES_MASTER 或者 KUBE_MASTER_IP是否进行设置，或者使用-h
	* 检查apiserver是否正常运行
	* 可以创建replicationController ，但是看不到pod
	* 检查controller是否在运行，查看logs
* kubectl挂起或者pod一直处于waiting状态
	* 检查pod分配到哪台node上了。如果还没有分配，说明他们还没有被scheduled，如果已经分配，则检查kubelete和docker日志。
	* 确保kubelet在etcd正确的地方查找pod。如果你看到像`DEBUG: get /registry/hosts/127.0.0.1/kubelet`这样的日志，需要查看apiserver是否使用了相同的名字，如果不是，请检查--hostname_override 指定的值。
	* 也可能是无法获取image，请查看docker日志
* 无法连接容器
	* telnet minion，查看是否可连接
	* 查看容器是否创建，使用docker ps -a
    
## 网络问题  
* minion无法与master建立连接
    * 查看ip addr, ip route show, traceroute kubernetes-master, and iptables --list的输出信息
        
  
参考：  
https://github.com/kubernetes/kubernetes/wiki/Debugging-FAQ