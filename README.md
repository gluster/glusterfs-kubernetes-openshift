
There are different ways to deploy and use GlusterFS in Kubernetes/openshift setup. 

### Deploying GlusterFS Pod:

GlusterFS pods can be deployed in Kubernetes/Openshift, so that Gluster Nodes are deployed in containers and it can provide persistent storage for  Openshift/Kubernetes setup.



The examples files in this repo are used for this demo.

Step 1: Create GlusterFS pod  

~~~
[root@atomic-node2 gluster_pod]# oc create -f gluster-1.yaml
~~~
Step 2: Get details about the GlusterFS pod.

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

~~~

From above logs you can see it pulled 'gluster/gluster-centos' container image and depoyed containers from it.

~~~
[root@atomic-node2 gluster_pod]# oc get pods
NAME        READY     STATUS    RESTARTS   AGE
gluster-1   1/1       Running   0          1m
~~~

Examine the container and make sure it has a running GlusterFS deamon.

~~~

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

Refer # http://www.humblec.com/persistent-volume-and-persistent-volume-claim-in-openshift-and-kubernetes-using-glusterfs-volume-plugin/

### Deploying GlusterFS Template 





~~~
[root@atomic-node2~]# oc create -f gluster-template.json
template "gluster1" created
[root@atomic-node2~]# oc process gluster1 -v NAME=gluster1 -v HOSTNAME=atomic-node2|oc create -f -
deploymentconfig "gluster1" created
[root@atomic-node2~]# oc get pods
NAME                READY     STATUS    RESTARTS   AGE
gluster1-1-deploy   1/1       Running   0          3s
[root@atomic-node2~]# oc describe pod gluster1-1-deploy
Name:				gluster1-1-deploy
Namespace:			default
Image(s):			openshift3/ose-deployer:v3.1.1.6
Node:				atomic-node2/10.70.43.183
Start Time:			Thu, 19 May 2016 13:32:34 +0530
Labels:				openshift.io/deployer-pod-for.name=gluster1-1
Status:				Running
Reason:				
Message:			
IP:				10.1.0.19
Replication Controllers:	<none>
Containers:
  deployment:
    Container ID:	docker://e6ffd0d1f369ad603803ed118e11b1855714ea57b47034b436b4325d7b67e00a
    Image:		openshift3/ose-deployer:v3.1.1.6
    Image ID:		docker://83d05f2929e92a7e8e9b62b33e408c1d83c3fe0e0de014475cb01c7d0fac7fb0
    QoS Tier:
      cpu:		BestEffort
      memory:		BestEffort
    State:		Running
      Started:		Thu, 19 May 2016 13:32:36 +0530
    Ready:		True
    Restart Count:	0
    Environment Variables:
      KUBERNETES_MASTER:	https://atomic-node2:8443
      OPENSHIFT_MASTER:		https://atomic-node2:8443
      BEARER_TOKEN_FILE:	/var/run/secrets/kubernetes.io/serviceaccount/token
      OPENSHIFT_CA_DATA:	-----BEGIN CERTIFICATE-----
MIIC5jCCAdCgAwIBAgIBATALBgkqhkiG9w0BAQswJjEkMCIGA1UEAwwbb3BlbnNo
aWZ0LXNpZ25lckAxNDU2ODIwMTYyMB4XDTE2MDMwMTA4MTYwMloXDTIxMDIyODA4
MTYwM1owJjEkMCIGA1UEAwwbb3BlbnNoaWZ0LXNpZ25lckAxNDU2ODIwMTYyMIIB
IjANBgkqhkiG9w0BAQEFAAOCAQ8AMIIBCgKCAQEA3XRxVLp7K/RJp2jezD3675Af
pLPbn4C4ZRDUM5v6gGF3mPboii40DYPY9ZxmX0fhnzidOp6jI3tmVXSsuBQszGi9
lNAvr+eqoz2Wf44EQhQ1+AtH6VcSFaGjGoMpkteskjuj9WV7qlGwS9uoql3ZwkEC
l8G+D95/opn691McWBe0KDebgl/Rpu+xk8T3ZR1rYSU4p3v0jD4Ir8bitiC/22wt
lq6f5UvhPU8jm+Y8xpaeFagnzZAgt+szycTymTIDQ1f66Qc/cIdS/WKzMqjyp5W1
6GKoNGrJm5KhqmqLnFOJ5Y9tzWCCbfTMHNo+fLWTpsk8uK3IR2cQRTp1hxpUBQID
AQABoyMwITAOBgNVHQ8BAf8EBAMCAKQwDwYDVR0TAQH/BAUwAwEB/zALBgkqhkiG
9w0BAQsDggEBAA310d2eeAApVoKNisDWTvPiuy9OIfVIyTe+Xcs63XM6vLVkNSSv
JKK/+HWdLF7AV4qlFY2azHsqThqwtReMKVBGo5CuCb7nOl6pTtGt15muFVIxccT0
m4mR0A49wpmCBB5DJA5sLA0RB8nA1pf8Q50KqMpUVIU6scoWAjHNHt+jHKVVXMSY
1enpa2CDeDW60uArAA/5UXvSW70bplF+EizlCfZ/XnAD5JiyZVNbptGKKja3eXrw
ZVkBBkN4IqXjSS98xur+LdQ4MS4K/S9xHXN1lhaWEQboiJD8cNOF0+XyWY9UCcUB
vIc6dUlEFaJaKB/JNUowfywiIQ/ABeFaiZ8=
-----END CERTIFICATE-----

      OPENSHIFT_DEPLOYMENT_NAME:	gluster1-1
      OPENSHIFT_DEPLOYMENT_NAMESPACE:	default
Conditions:
  Type		Status
  Ready 	True 
Volumes:
  deployer-token-tvmcp:
    Type:	Secret (a secret that should populate this volume)
    SecretName:	deployer-token-tvmcp
Events:
  FirstSeen	LastSeen	Count	From						SubobjectPath				Reason		Message
  ─────────	────────	─────	────						─────────────				──────		───────
  12s		12s		1	{scheduler }										Scheduled	Successfully assigned gluster1-1-deploy to atomic-node2
  11s		11s		1	{kubelet atomic-node2}	implicitly required container POD	Pulled		Container image "openshift3/ose-pod:v3.1.1.6" already present on machine
  11s		11s		1	{kubelet atomic-node2}	implicitly required container POD	Created		Created with docker id 84751e8ffe5e
  10s		10s		1	{kubelet atomic-node2}	implicitly required container POD	Started		Started with docker id 84751e8ffe5e
  10s		10s		1	{kubelet atomic-node2}	spec.containers{deployment}		Pulled		Container image "openshift3/ose-deployer:v3.1.1.6" already present on machine
  10s		10s		1	{kubelet atomic-node2}	spec.containers{deployment}		Created		Created with docker id e6ffd0d1f369
  10s		10s		1	{kubelet atomic-node2}	spec.containers{deployment}		Started		Started with docker id e6ffd0d1f369


[root@atomic-node2~]# oc get pods
NAME                READY     STATUS    RESTARTS   AGE
gluster1-1-deploy   1/1       Running   0          16s
gluster1-1-k1dv6    1/1       Running   0          12s

[root@atomic-node2~]# oc get pods
NAME               READY     STATUS    RESTARTS   AGE
gluster1-1-k1dv6   1/1       Running   0          32s
[root@atomic-node2~]# oc describe pod gluster1-1-k1dv6
Name:				gluster1-1-k1dv6
Namespace:			default
Image(s):			gluster/gluster-centos
Node:				atomic-node2/10.70.43.183
Start Time:			Thu, 19 May 2016 13:32:38 +0530
Labels:				deployment=gluster1-1,deploymentconfig=gluster1,name=gluster1
Status:				Running
Reason:				
Message:			
IP:				10.1.0.20
Replication Controllers:	gluster1-1 (1/1 replicas created)
Containers:
  glusterfs:
    Container ID:	docker://12432af860908e9eab3b1d64d8abb4654bf5d23baa787389a3aecbf30308b63a
    Image:		gluster/gluster-centos
    Image ID:		docker://7dd2be8087cbc54bcac76a2f220d6f0cef7992dc6966cdf52611d12f7e01a823
    QoS Tier:
      cpu:		BestEffort
      memory:		BestEffort
    State:		Running
      Started:		Thu, 19 May 2016 13:32:39 +0530
    Ready:		True
    Restart Count:	0
    Environment Variables:
Conditions:
  Type		Status
  Ready 	True 
Volumes:
  glusterfs-etc:
    Type:	HostPath (bare host directory volume)
    Path:	/etc/glusterfs
  glusterfs-logs:
    Type:	HostPath (bare host directory volume)
    Path:	/var/log/glusterfs
  glusterfs-config:
    Type:	HostPath (bare host directory volume)
    Path:	/var/lib/glusterd
  glusterfs-dev:
    Type:	HostPath (bare host directory volume)
    Path:	/dev
  glusterfs-cgroup:
    Type:	HostPath (bare host directory volume)
    Path:	/sys/fs/cgroup
  default-token-72d89:
    Type:	Secret (a secret that should populate this volume)
    SecretName:	default-token-72d89
Events:
  FirstSeen	LastSeen	Count	From						SubobjectPath				Reason		Message
  ─────────	────────	─────	────						─────────────				──────		───────
  41s		41s		1	{scheduler }										Scheduled	Successfully assigned gluster1-1-k1dv6 to atomic-node2
  41s		41s		1	{kubelet atomic-node2}	implicitly required container POD	Pulled		Container image "openshift3/ose-pod:v3.1.1.6" already present on machine
  41s		41s		1	{kubelet atomic-node2}	implicitly required container POD	Created		Created with docker id ca1ff8adf07c
  41s		41s		1	{kubelet atomic-node2}	implicitly required container POD	Started		Started with docker id ca1ff8adf07c
  40s		40s		1	{kubelet atomic-node2}	spec.containers{glusterfs}		Pulled		Container image "gluster/gluster-centos" already present on machine
  40s		40s		1	{kubelet atomic-node2}	spec.containers{glusterfs}		Created		Created with docker id 12432af86090
  40s		40s		1	{kubelet atomic-node2}	spec.containers{glusterfs}		Started		Started with docker id 12432af86090


[root@atomic-node2~]# oc get pods
NAME               READY     STATUS    RESTARTS   AGE
gluster1-1-k1dv6   1/1       Running   0          44s
[root@atomic-node2~]# oc get pods
NAME               READY     STATUS    RESTARTS   AGE
gluster1-1-k1dv6   1/1       Running   0          46s
[root@atomic-node2~]# oc get pods
NAME               READY     STATUS    RESTARTS   AGE
gluster1-1-k1dv6   1/1       Running   0          2m
[root@atomic-node2~]# oc get pods
NAME               READY     STATUS    RESTARTS   AGE
gluster1-1-k1dv6   1/1       Running   0          2m
[root@atomic-node2~]# oc edit pod gluster1-1-k1dv6
Edit cancelled, no changes made.
[root@atomic-node2~]# oc exec -ti gluster1-1-k1dv6 /bin/bash
[root@gluster1-1-k1dv6 /]# 
[root@gluster1-1-k1dv6 /]# 
[root@gluster1-1-k1dv6 /]# 
[root@gluster1-1-k1dv6 /]# systemctl status glusterd
● glusterd.service - GlusterFS, a clustered file-system server
   Loaded: loaded (/usr/lib/systemd/system/glusterd.service; enabled; vendor preset: disabled)
   Active: active (running) since Thu 2016-05-19 04:02:45 EDT; 3min 16s ago
  Process: 216 ExecStart=/usr/sbin/glusterd -p /var/run/glusterd.pid --log-level $LOG_LEVEL $GLUSTERD_OPTIONS (code=exited, status=0/SUCCESS)
 Main PID: 219 (glusterd)
   CGroup: /system.slice/docker-12432af860908e9eab3b1d64d8abb4654bf5d23baa787389a3aecbf30308b63a.scope/system.slice/glusterd.service
           └─219 /usr/sbin/glusterd -p /var/run/glusterd.pid --log-level INFO...

May 19 04:02:40 gluster1-1-k1dv6 systemd[1]: Starting GlusterFS, a clustered....
May 19 04:02:45 gluster1-1-k1dv6 systemd[1]: Started GlusterFS, a clustered ....
Hint: Some lines were ellipsized, use -l to show in full.
[root@gluster1-1-k1dv6 /]# gluster v info
No volumes present
[root@gluster1-1-k1dv6 /]# service sshd status
Redirecting to /bin/systemctl status  sshd.service
● sshd.service - OpenSSH server daemon
   Loaded: loaded (/usr/lib/systemd/system/sshd.service; enabled; vendor preset: enabled)
   Active: active (running) since Thu 2016-05-19 04:02:40 EDT; 3min 38s ago
     Docs: man:sshd(8)
           man:sshd_config(5)
 Main PID: 217 (sshd)
   CGroup: /system.slice/docker-12432af860908e9eab3b1d64d8abb4654bf5d23baa787389a3aecbf30308b63a.scope/system.slice/sshd.service
           └─217 /usr/sbin/sshd -D

May 19 04:02:40 gluster1-1-k1dv6 systemd[1]: Started OpenSSH server daemon.
May 19 04:02:40 gluster1-1-k1dv6 systemd[1]: Starting OpenSSH server daemon...
May 19 04:02:40 gluster1-1-k1dv6 sshd[217]: Server listening on 0.0.0.0 port 22.
May 19 04:02:40 gluster1-1-k1dv6 sshd[217]: Server listening on :: port 22.
Hint: Some lines were ellipsized, use -l to show in full.
[root@gluster1-1-k1dv6 /]#  cat /etc/redhat-release
CentOS Linux release 7.2.1511 (Core)
[root@gluster1-1-k1dv6 /]# exit
exit
[root@atomic-node2~]# 
~~~


