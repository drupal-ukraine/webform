Drupal Vagrant Dev box for CIbox support
======

#Installation
* [Vagrant](https://www.vagrantup.com/downloads.html)
* [VirtualBox](https://www.virtualbox.org/wiki/Downloads)
* Useful Vagrant's plugins
  * [Hosts Updater](https://github.com/cogitatio/vagrant-hostsupdater)
  * [vbguest](https://github.com/dotless-de/vagrant-vbguest)


#Usage

```sh
vagrant up --provision && vagrant ssh
```

**Drupal 7 reinstallation from scratch**

Unix users
```sh
cd /var/www/d7
```

```sh
sh reinstall.sh
```
Windows users
```sh
sh reinstall.sh --windows
```
By default your site will be accessible by using this url. 

```
http://drupal7.192.168.56.132.xip.io/
```

**Drupal 8 reinstallation from scratch**

Open `http://drupal8.192.168.56.132.xip.io` and go through installation process.

If ```xip.io``` not working - create row with

```hosts
192.168.56.112 drupal7.192.168.56.132.xip.io drupal8.192.168.56.132.xip.io
```

in ```/etc/hosts``` or just use another ServerName in apache.yml

If you have Vagrant HostUpdater plugin, your hosts file will be automatically updated.

VirtualBox additions
=====
For automatic update additions within guest, please install proper plugin

```sh
vagrant plugin install vagrant-vbguest
```


Tools
=====

* XDebug
* Drush
* Selenium 2
* Composer
* Adminer
* XHProf
* PHP Daemon
* PHP, SASS, JS sniffers/lints/hints

##Adminer
Adminer for mysql administration (credentials drupal:drupal and root:root)

```
http://192.168.56.112.xip.io/adminer.php
```

##PHP Profiler XHProf
It is installed by default, but to use it as Devel module integration use:
```sh
drush en devel -y
drush vset devel_xhprof_enabled 1
drush vset devel_xhprof_directory '/usr/share/php' && drush vset devel_xhprof_url '/xhprof_html/index.php'
ln -s /usr/share/php/xhprof_html xhprof_html
```
After `vset devel_xhprof_enabled` it could return an error about "Class 'XHProfRuns_Default' not found" - ignore it.


Windows Containers
=====

Install [Cygwin](https://servercheck.in/blog/running-ansible-within-windows) according to provided steps.

Run Cygwin as Administrator user.

Use default flow to up Vagrant but run `sh reinstall.yml --windows`

##Windows troubleshooting

If you will see error liek ```...[error 26] file is busy...``` during ```sh reinstall.sh``` modify that line:

before

```yml
name: Stage File Proxy settings
sudo: yes
lineinfile: dest='sites/default/settings.php' line='$conf[\"stage_file_proxy_origin\"] = \"{{ stage_file_proxy_url }}";'
```

after:

```yml
name: Copy settings.php
sudo: yes
shell: cp sites/default/settings.php /tmp/reinstall_settings.php

name: Stage File Proxy settings
sudo: yes
lineinfile: dest='sites/default/settings.php' line='$conf[\"stage_file_proxy_origin\"] = \"{{ stage_file_proxy_url }}\";'

name: Restore settings.php
sudo: yes
shell: cp /tmp/reinstall_settings.php sites/default/settings.php
```
