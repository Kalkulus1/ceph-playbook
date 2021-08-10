# A ceph playbook

## Current Setup

Spin up 8 instances with the appropriate rules

Name the instances
```
Managers: mgr
Monitors: mon
OSDs: osd1, osd2, osd3
MDSs: mds
Grafana Server: gfs
Client: client
```

## Manager configuration
We need ssh-keys to be able to communicate with the servers.

We can `generate` one or simply use what we generated from the `console`.

### Console Keys
AWS generated a key pair and gave us the private key but stored the public key on the servers, specifically in the location `~/.ssh/authorized_keys`

We will use the authorized_keys as the public key and the private key as what we have on our local machine. 

Run the commands below

```sh
cp ~/.ssh/authorized_keys ~/.ssh/id_rsa.pub
```

Display the content of the private on your local machine

```sh
cat keyPair.pem
```

Copy what you see from beginning to the end.. eg. below

```sh
-----BEGIN RSA PRIVATE KEY-----
MIIEpAIBAAKCAQEAoSgo62bxaNAEgE77r6Qgj7w2gL01WkaJtpZxkwF5o6/2s6EY
FcPmWbviyHROeXtVuV3H69l7dccN/uxjDINDQkEYSG8rkGdes38WRlYeiwCR0528
2CS9fRqRqk3I27ZOjQcI9r7rc6/MxK7rnsrlw3psvtLxi/ARM+EvKViWZTPoSB++
aPqsUX/zS4D2WB+pz6V3uWwZPNehnHcxPXw+E06pHxW7k8PX3i3MGiPUkrBUJyMQ
vf1wJ3hwSp4EGQuug9NCj0cllOxBYS39vDsdmo2F9eSciLNGMCRazfWDzm/verRw
YsVG7/XNehoD0lZiad3pJBl2yb2jSc8FM7gqNwIDAQABAoIBAQCa5rAWPHeUSEXb
Uo/G7ppBriYxLiynHh/mecqnA4b41/zoAdLVsNurqtW72VdIm0z1hHfScdOijHce
bfZyo4S1nT4K+ZhFD6bOHzy41LQ85YpFflty4jWklzImIOgMj7oHAhkSmsZUB0lq
T7u4gMvCTd1Sug1kdZcDZ9HeCwFcObQHnh44lGZFclrR6lixk4f0q9Qtz3zt7/mX
NHxMIWnFkAK3Fxhkoq5jGBl8rZKqXSxSt1gWiLrtYdBu1kJES1ZV0YhO7Jl2kZ46
ShcAlASsJ3fJV3jCApBgn8lsZAl/J3ycZ5HXQCj1ZWVWpCDYI7w6GFjRyfDViaLn
mBMnmQNhAoGBAOWS6X894SD9b/OeOyqq0ZUTqkfEV4xLpmpcPpYPXsq2ZEvTbISy
etFMens6oio3PyoAcjsgQdKffOVPH9kbn2aHfO0+APGCtQNb2WODgDOl8YvrrH0b
uKiai/SIJk8xqRquKbY+vQtMmR7iXPCjYPde+ivZZnK9/2OvYV9EPENHAoGBALO1
IlNx0WjsdCptretkbfCtnKooU/UPBJZnPzIjCr6kvx95dEu5nvD/pwxu38K3e59Y
+8EIY0NqtgenRd6kdwDdtTSc8fsMnYdLSjvvdhHVk5GTt7fOLMy0fBPjQLlfXt1E
yP5tjIR9fRv6SY0iFwFQz7241lIg6ADwuvUlqvmRAoGAIG/9pgjsQmkM5HM7EPBJ
5LJ+oN1RMXWO8gBOp41Ma4AR89TPxIAjcmdH9xl1yFk+B4VAnrIUGtqGHzBq45ZP
k4EZWbwlDfrV8L+Nm838009jLc4C9TpxgQE1PTvvjckKh8aMBkAfHfWYvRBm6Wls
TgeReZiiLRxDdaZWL8vIaAkCgYEAiMfDerPU6gGfYXPdvp7QNzJNXg4X6RwnJ7sq
FKUrVnParMAqzkBYQ6KyqPnzAifZxi1aYVBbDUM5jZW5zn5rvZMCiysx2x19AS9D
L19SBL0E9vbyDMq2upBdKF3+M5ntgIkMWutS2r0Q7qyQMwXRcMTrecIoDpbRP3X7
NwAWpKECgYAmpCsD44gO/HSRQTW5D6t7EkaY/SjKCylembf4L56ZKkxECgWxz8Wb
14hrXess3vH9PcfGnH3CAZCy3oUGzAwjuxjwEcQB9HnM9liEVsNlVhKS6TVGK71E
AX4VbAe1afSzMmU1TF1RJspffsR8S357l3KCyXYFJbrbw5LHVPyomQ==
-----END RSA PRIVATE KEY-----
```

Head onto the manager

```sh
nano ~/.ssh/id_rsa
```

Past the content there. Save the file and exit

Change the file permissions

```ssh
chmod 400 ~/.ssh/id_rsa
```

The next thing is to add all hosts to the managers host file

```sh
sudo nano /etc/hosts
```
```sh
172.31.23.65 mgr
172.31.24.49 mon
172.31.21.226 osd1
172.31.23.115 osd2
172.31.18.39 osd3
172.31.23.73 gfs
172.31.20.221 mds
172.31.18.1 client
```

Get your right ip address for each server

Try to connect to servers

```sh
ssh mon
exit
ssh osd1
exit
ssh osd2
exit
ssh osd3
exit
ssh gfs
exit
ssh mds
exit
ssh client
exit
```

Copy below and add to /etc/hosts of mon, osd1, osd2, osd3, mds and gfs

```sh
172.31.23.65 mgr
172.31.24.49 mon
172.31.21.226 osd1
172.31.23.115 osd2
172.31.18.39 osd3
172.31.23.73 gfs
172.31.20.221 mds
```

Specify the right IP addresses

Try to establish connection between servers

### Add the key-pair to mon, osd1, osd2, osd3, mds and gfs
```sh
scp /home/ubuntu/.ssh/* ubuntu@mon:/home/ubuntu/.ssh/.
scp /home/ubuntu/.ssh/* ubuntu@osd1:/home/ubuntu/.ssh/.
scp /home/ubuntu/.ssh/* ubuntu@osd2:/home/ubuntu/.ssh/.
scp /home/ubuntu/.ssh/* ubuntu@osd3:/home/ubuntu/.ssh/.
scp /home/ubuntu/.ssh/* ubuntu@mds:/home/ubuntu/.ssh/.
scp /home/ubuntu/.ssh/* ubuntu@gfs:/home/ubuntu/.ssh/.
```

You are seeing ubuntu because ubuntu is my username on all the servers.

## Ceph Configuration on the manager (mgr)

Let's first install ansible.

```sh
sudo add-apt-repository ppa:ansible/ansible
sudo apt update
sudo apt install ansible
```

Clone the ceph ansible repo

```sh
cd ~
git clone https://github.com/ceph/ceph-ansible.git
cd ceph-ansible
git checkout stable-5.0
```

```sh
cp group_vars/mgrs.yml.example group_vars/mgrs.yml
cp group_vars/mons.yml.example group_vars/mons.yml
cp group_vars/all.yml.example group_vars/all.yml
cp group_vars/osds.yml.example group_vars/osds.yml
```

Copy the content of `osds.yml` and `all.yml` with what's in this repo. Make sure to configure your network address and other configurations

```yml
#all.yml
monitor_interface: eth0
cluster_network: 172.31.0.0/16
radosgw_interface: eth0
```

```yml
#osds.yml
devices:
  - /dev/xvdb
  - /dev/xvda
#  - /dev/sdb
#  - /dev/sdc
#  - /dev/sdd
#  - /dev/sde
```

Let's create create a host file in ceph-ansible

```sh
nano hosts
```

Copy the content below into it

```sh
#Ceph admin user for ssh and sudo
[all:vars]
ansible_ssh_user=ubuntu
ansible_become=true
ansible_become_method=sudo
ansible_become_user=root

[mons]
mon

[mdss]
mds

[rgws]
mon

[osds]
osd1
osd2
osd3

[grafana-server]
gfs

```

Get the site.yml file

```sh
cp site.yml site.yml.example
```

Install some dependies

```sh
sudo apt install python-netaddr
```

Run the playbook

```sh
ansible-playbook -i hosts site.yml
```

### Add the client
