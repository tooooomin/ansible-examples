  ## Vaigrant install and setup
### vagrnt起動方法
* http://easyramble.com/install-vagrant-virtualbox-on-mac.html

### Vagrantのssh接続設定
VagrantFileの中で
```
$ pwd
~/Vagrant/CentOS64
$ vagrant ssh-config
Host default
  HostName 127.0.0.1
  User vagrant
  Port 2200
  UserKnownHostsFile /dev/null
  StrictHostKeyChecking no
  PasswordAuthentication no
  IdentityFile USER_HOME/.vagrant.d/insecure_private_key
  IdentitiesOnly yes
  LogLevel FATAL
```
`~/.ssh/config`　に下記を追加
```
Host ansible-test // hostName
       HostName 127.0.0.1
       User vagrant
       Port 2222
       UserKnownHostsFile /dev/null
       StrictHostKeyChecking no
       PasswordAuthentication no
       IdentityFile USER_HOME/.vagrant.d/insecure_private_key
       IdentitiesOnly yes
       LogLevel FATAL
```
接続
```
$ ssh ansible-test
```

## Install ansible

* mac

```
sudo pip install ansible
```

* sentOS

```
[vagrant@vagrant-centos65 ~]$ sudo rpm -ivh
http://ftp.riken.jp/Linux/fedora/epel/6/i386/epel-release-6-8.noarch.rpm
Retrieving http://ftp.riken.jp/Linux/fedora/epel/6/i386/epel-release-6-8.noarch.rpm
Preparing...                ########################################### [100%]
	package epel-release-6-8.noarch is already installed
```
```
[vagrant@vagrant-centos65 ~]$ sudo yum install ansible
Failed to set locale, defaulting to C
Loaded plugins: fastestmirror, versionlock
Loading mirror speeds from cached hostfile
 * base: ftp.riken.jp
 * epel: ftp.iij.ad.jp
 * extras: ftp.riken.jp
 * updates: ftp.riken.jp
Setting up Install Process
Resolving Dependencies
--> Running transaction check
---> Package ansible.noarch 0:1.9.1-1.el6 will be installed
--> Processing Dependency: python-simplejson for package: ansible-1.9.1-1.el6.noarch
....
Complete!
```

### check connection
```
$ ansible ansible-test -m ping -i ansible-test
ansible-test | success >> {
    "changed": false,
    "ping": "pong"
}
```

## Running ansible
http://yteraoka.github.io/ansible-tutorial/#test-ansible

`mysql.yml`
```yaml
- hosts: ansible-test
  sudo: yes
  tasks:
    - name: install mysql
      yum: name=mysql state=installed
    - name: install mysql-server
      yum: name=mysql-server state=installed
    - name: install mysql-devel
      yum: name=mysql-devel state=installed
```
### 実行確認
* ansible-test -> `~/.ssh/config`に設定したhostname

```
$ ansible-playbook -i ansible-test mysql.yml --check
```
```yml
PLAY [ansible-test] ***********************************************************

GATHERING FACTS ***************************************************************
ok: [ansible-test]

TASK: [install mysql] *********************************************************
changed: [ansible-test]

TASK: [install mysql-server] **************************************************
changed: [ansible-test]

TASK: [install mysql-devel] ***************************************************
changed: [ansible-test]

PLAY RECAP ********************************************************************
ansible-test               : ok=4    changed=3    unreachable=0    failed=0
```

### 実行
```
$ ansible-playbook -i ansible-test mysql.yml
```
