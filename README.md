# glusterfs-k8s-ose
GlusterFS + Kubernetes + Openshift 

### Deploying GlusterFS Pod:


~~~
[root@atomic-node2 gluster_pod]# oc describe pod gluster-1
Name:				gluster-1
Namespace:			default
Image(s):			gluster/gluster-centos
Node:				atomic-node1/10.70.43.174
Start Time:			Tue, 17 May 2016 10:19:17 +0530
Labels:				name=gluster-1
Status:				Running
Reason:				
Message:			
IP:				10.70.43.174
Replication Controllers:	<none>
Containers:
  glusterfs:
    Container ID:	docker://ff8f4af700d725dfe0e08939ec011c34ddf9dedc7204e0ced1cc355a56150742
    Image:		gluster/gluster-centos
    Image ID:		docker://033de9c44a8aabde55ce8a2b751ccf5bc345fdb534ea30e79a8fa70b82dc7761
    QoS Tier:
      cpu:		BestEffort
      memory:		BestEffort
    State:		Running
      Started:		Tue, 17 May 2016 10:20:35 +0530
    Ready:		True
    Restart Count:	0
    Environment Variables:
Conditions:
  Type		Status
  Ready 	True 
Volumes:
  brickpath:
    Type:	HostPath (bare host directory volume)
    Path:	/mnt/brick1
  default-token-72d89:
    Type:	Secret (a secret that should populate this volume)
    SecretName:	default-token-72d89
Events:
  FirstSeen	LastSeen	Count	From						SubobjectPath				Reason		Message
  ─────────	────────	─────	────						─────────────				──────		───────
  1m		1m		1	{scheduler }										Scheduled	Successfully assigned gluster-1 to atomic-node1
  1m		1m		1	{kubelet atomic-node1}	implicitly required container POD	Pulled		Container image "openshift3/ose-pod:v3.1.1.6" already present on machine
  1m		1m		1	{kubelet atomic-node1}	implicitly required container POD	Created		Created with docker id f55ce55e6ea3
  1m		1m		1	{kubelet atomic-node1}	implicitly required container POD	Started		Started with docker id f55ce55e6ea3
  1m		1m		1	{kubelet atomic-node1}	spec.containers{glusterfs}		Pulling		pulling image "gluster/gluster-centos"
  8s		8s		1	{kubelet atomic-node1}	spec.containers{glusterfs}		Pulled		Successfully pulled image "gluster/gluster-centos"
  8s		8s		1	{kubelet atomic-node1}	spec.containers{glusterfs}		Created		Created with docker id ff8f4af700d7
  8s		8s		1	{kubelet atomic-node1}	spec.containers{glusterfs}		Started		Started with docker id ff8f4af700d7


[root@atomic-node2 gluster_pod]# oc get pods
NAME        READY     STATUS    RESTARTS   AGE
gluster-1   1/1       Running   0          1m
[root@atomic-node2 gluster_pod]# oc exec -ti gluster-1 /bin/bash
[root@atomic-node1 /]# 
[root@atomic-node1 /]# 
[root@atomic-node1 /]# ps aux  
USER        PID %CPU %MEM    VSZ   RSS TTY      STAT START   TIME COMMAND
root          1  0.4  0.0  40780  2920 ?        Ss   04:50   0:00 /usr/sbin/init
root         20  0.3  0.0  36816  4272 ?        Ss   04:50   0:00 /usr/lib/syste
root         21  0.0  0.0 118476  1332 ?        Ss   04:50   0:00 /usr/sbin/lvme
root         37  0.0  0.0 101344  1228 ?        Ssl  04:50   0:00 /usr/sbin/gssp
rpc          44  0.1  0.0  64904  1052 ?        Ss   04:50   0:00 /sbin/rpcbind 
root        209  0.1  0.1 364716 13444 ?        Ssl  04:50   0:00 /usr/sbin/glus
root        341  1.1  0.0  13368  1964 ?        Ss   04:51   0:00 /bin/bash
root        354  0.0  0.0  49020  1820 ?        R+   04:51   0:00 ps aux
[root@atomic-node1 /]# service glusterd status
Redirecting to /bin/systemctl status  glusterd.service
● glusterd.service - GlusterFS, a clustered file-system server
   Loaded: loaded (/usr/lib/systemd/system/glusterd.service; enabled; vendor preset: disabled)
   Active: active (running) since Tue 2016-05-17 04:50:41 UTC; 35s ago
  Process: 208 ExecStart=/usr/sbin/glusterd -p /var/run/glusterd.pid --log-level $LOG_LEVEL $GLUSTERD_OPTIONS (code=exited, status=0/SUCCESS)
 Main PID: 209 (glusterd)
   CGroup: /system.slice/docker-ff8f4af700d725dfe0e08939ec011c34ddf9dedc7204e0ced1cc355a56150742.scope/system.slice/glusterd.service
           └─209 /usr/sbin/glusterd -p /var/run/glusterd.pid --log-level INFO...
           ‣ 209 /usr/sbin/glusterd -p /var/run/glusterd.pid --log-level INFO...

May 17 04:50:36 atomic-node1 systemd[1]: Starting Gluste...
May 17 04:50:41 atomic-node1 systemd[1]: Started Gluster...
Hint: Some lines were ellipsized, use -l to show in full.
[root@atomic-node1 /]# gluster --version
glusterfs 3.7.9 built on Mar 20 2016 03:19:49
Repository revision: git://git.gluster.com/glusterfs.git
Copyright (c) 2006-2011 Gluster Inc. <http://www.gluster.com>
GlusterFS comes with ABSOLUTELY NO WARRANTY.
You may redistribute copies of GlusterFS under the terms of the GNU General Public License.
[root@atomic-node1 /]# 

[root@atomic-node1 /]# mount |grep mnt 
/dev/mapper/atomic-node1-root on /mnt/brick1 type xfs (rw,relatime,seclabel,attr2,inode64,noquota)
[root@atomic-node1 /]# ip a
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN 
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
    inet6 ::1/128 scope host 
       valid_lft forever preferred_lft forever
2: eth0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc pfifo_fast state UP qlen 1000
    link/ether 52:54:00:f3:4b:e3 brd ff:ff:ff:ff:ff:ff
    inet 10.70.43.174/22 brd 10.70.43.255 scope global dynamic eth0
       valid_lft 62555sec preferred_lft 62555sec
    inet6 2620:52:0:4628:5054:ff:fef3:4be3/64 scope global noprefixroute dynamic 
       valid_lft 2591620sec preferred_lft 604420sec
    inet6 fe80::5054:ff:fef3:4be3/64 scope link 
       valid_lft forever preferred_lft forever
3: ovs-system: <BROADCAST,MULTICAST> mtu 1500 qdisc noop state DOWN 
    link/ether c6:b6:3b:94:a7:88 brd ff:ff:ff:ff:ff:ff
5: br0: <BROADCAST,MULTICAST> mtu 1450 qdisc noop state DOWN 
    link/ether 4a:5f:b9:50:89:46 brd ff:ff:ff:ff:ff:ff
7: lbr0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1450 qdisc noqueue state UP 
    link/ether f2:2c:ed:a7:d1:6b brd ff:ff:ff:ff:ff:ff
    inet 10.1.3.1/24 scope global lbr0
       valid_lft forever preferred_lft forever
    inet6 fe80::d070:feff:fe82:afe4/64 scope link 
       valid_lft forever preferred_lft forever
8: vovsbr@vlinuxbr: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1450 qdisc pfifo_fast master ovs-system state UP 
    link/ether 96:38:12:0f:c2:e2 brd ff:ff:ff:ff:ff:ff
    inet6 fe80::9438:12ff:fe0f:c2e2/64 scope link 
       valid_lft forever preferred_lft forever
9: vlinuxbr@vovsbr: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1450 qdisc pfifo_fast master lbr0 state UP 
    link/ether f2:2c:ed:a7:d1:6b brd ff:ff:ff:ff:ff:ff
    inet6 fe80::f02c:edff:fea7:d16b/64 scope link 
       valid_lft forever preferred_lft forever
10: tun0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1450 qdisc noqueue state UNKNOWN 
    link/ether b2:a8:c0:73:3a:be brd ff:ff:ff:ff:ff:ff
    inet 10.1.3.1/24 scope global tun0
       valid_lft forever preferred_lft forever
    inet6 fe80::b0a8:c0ff:fe73:3abe/64 scope link 
       valid_lft forever preferred_lft forever
[root@atomic-node1 /]# 
[root@atomic-node1 /]# cat /etc/redhat-release 
CentOS Linux release 7.2.1511 (Core) 
[root@atomic-node1 /]# 


[root@atomic-node2 gluster_pod]# oc get pods
NAME        READY     STATUS    RESTARTS   AGE
gluster-1   1/1       Running   0          3m
[root@atomic-node2 gluster_pod]# 



~~~

### Deploying GlusterFS PV, PVC, SERVICE and ENDPOINT

Refer # http://tinyurl.com/hne8g7o

### Deploying GlusterFS Template 




