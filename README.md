# Setup 1: Using Ceph Ansible

## Current Setup

Spin up 9 instances with the appropriate rules using AWS Console

```
AMI: Ubuntu Server 18.04 LTS (HVM), SSD Volume Type
Instance Type: t2.medium
Instance Details: For learning purpose, set number of instances to 9
Add Storage: For learning sake, add more volumes and set the sizes to 50GB (Sandbox limit)
Security Group: SSH, ICMP, TCP 8080, TCP 9283 (For learning sake, I allowed All Traffic so I dont have to worry about the ports to open)
```

Name the instances
```
Admin: admin
Managers: mgr
Monitors: mon
OSDs: osd1, osd2, osd3
MDSs: mds
Grafana Server: gfs
Client: client
```

## Admin configuration
We need ssh-keys to be able to communicate with the servers.

We can `generate` one or simply use what we generated from the `AWS console`.

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

Head onto the admin

```sh
nano ~/.ssh/id_rsa
```

Past the content there. Save the file and exit

Change the file permissions

```ssh
chmod 400 ~/.ssh/id_rsa
```

The next thing is to add all hosts to the admin host file

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

Get the right ip address for each server

Try to connect to servers

```sh
ssh mgr
exit
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

You are seeing ubuntu because ubuntu is my username on all the servers.

### Ceph Configuration setup on the admin

Let's first install ansible. Ansible would help us automate the installation and configuration of ceph on all our nodes.

Add the package repository
```sh
sudo add-apt-repository ppa:ansible/ansible
```

Install ansible now:

```sh
sudo apt update
sudo apt install ansible -y
```

Install another dependency package
```sh
sudo apt install python-netaddr -y
```

### Adding configuration files
Clone the ceph ansible repo

```sh
cd ~
git clone https://github.com/ceph/ceph-ansible.git
cd ceph-ansible
git checkout stable-5.0
```

Copy and paste the content of the following files. Read all YML files

```sh
nano group_vars/all.yml
nano group_vars/clients.yml
nano group_vars/mdss.yml
nano group_vars/mgrs.yml
nano group_vars/mons.yml
nano group_vars/osds.yml
```

For the `osds.yml`, take not of the devices added:
```yml
#osds.yml
devices:
  - /dev/xvda
  - /dev/xvdb
  - /dev/xvdc
  - /dev/xvdd
  - /dev/xvde
#  - /dev/sdb
#  - /dev/sdc
#  - /dev/sdd
#  - /dev/sde
```

For the `all.yml` file, take note of the ff:
```yml
#all.yml
monitor_interface: eth0
cluster_network: 172.31.0.0/16
radosgw_interface: eth0
```

Get the lists of hosts
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

[mgrs]
mgr

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

[clients]
client
```

Let's ping all the hosts using ansible:

```sh
ansible all -m ping -i hosts
```

Output:
```sh
client | SUCCESS => {
    "changed": false, 
    "ping": "pong"
}
mon | SUCCESS => {
    "changed": false, 
    "ping": "pong"
}
osd2 | SUCCESS => {
    "changed": false, 
    "ping": "pong"
}
mds | SUCCESS => {
    "changed": false, 
    "ping": "pong"
}
gfs | SUCCESS => {
    "changed": false, 
    "ping": "pong"
}
mgr | SUCCESS => {
    "changed": false, 
    "ping": "pong"
}
osd3 | SUCCESS => {
    "changed": false, 
    "ping": "pong"
}
osd1 | SUCCESS => {
    "changed": false, 
    "ping": "pong"
}
```

Get the site.yml file

```sh
cp site.yml.sample site.yml
```

Run the playbook

```sh
ansible-playbook -i hosts site.yml
```

### Dashboard

Login to the mon from your admin

```sh
ssh mon
```

Verify the dashboard url

```sh
sudo ceph mgr services
```

Output
```json
{
    "dashboard": "https://mgr:8080/",
    "prometheus": "http://mgr:9283/"
}
```

When all is done, and works successfully with no errors, you can locate the dashboard running on the manager... 

```sh
http://mgr:8080
```

Replace mgr in the url with the public ip address of the monitor. eg. `http://34.56.78.345:8080`

login with the username and password specified in the `all.yml` file...

```sh
admin
p@ssw0rd
```


#### Orchestrator
Enabling the orchestrator will allow have other functionalities on the dashboard. 

```sh
ssh mon
sudo ceph mgr module enable test_orchestrator
sudo ceph orch set backend test_orchestrator
sudo ceph orch status
```