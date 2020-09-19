# logging
В vagrant файле создаются 2 ВМ, одна из которых собирает внутренние логи и отправляет их на удалённую машину.
Внутри каталога files находятся конфиги для нджинкса и рсислога.
# 1. Web-сервер
nginx генерит лог доступа и лог ошибок, отправляя их на сервер 192.168.0.230 (/mnt/logging/192.168.0.220)
```
[vagrant@log 192.168.0.220]$ ll
total 28
-rw-------. 1 root root   62 Sep 19 17:13 audisp-remote.log
-rw-------. 1 root root  129 Sep 19 17:18 nginx_access.log
-rw-------. 1 root root 1612 Sep 19 17:17 sshd.log
-rw-------. 1 root root  816 Sep 19 17:17 sudo.log
-rw-------. 1 root root  657 Sep 19 17:17 su.log
-rw-------. 1 root root  654 Sep 19 17:17 systemd.log
-rw-------. 1 root root  421 Sep 19 17:17 systemd-logind.log
```
Логи аудита смотрим командой ausearch -ts today -i | grep nginx
```
[vagrant@log ~]$ sudo ausearch -ts today -i | grep nginx
node=web type=CONFIG_CHANGE msg=audit(09/19/2020 17:15:57.718:1620) : auid=vagrant ses=5 op=updated_rules path=/etc/nginx/nginx.conf key=web_config_changed list=exit res=yes
node=web type=PROCTITLE msg=audit(09/19/2020 17:15:57.718:1621) : proctitle=vi /etc/nginx/nginx.conf
node=web type=PATH msg=audit(09/19/2020 17:15:57.718:1621) : item=3 name=/etc/nginx/nginx.conf~ inode=67149891 dev=08:01 mode=file,644 ouid=root ogid=root rdev=00:00 obj=unconfined_u:object_r:user_tmp_t:s0 objtype=CREATE cap_fp=none cap_fi=none cap_fe=0 cap_fver=0
node=web type=PATH msg=audit(09/19/2020 17:15:57.718:1621) : item=2 name=/etc/nginx/nginx.conf inode=67149891 dev=08:01 mode=file,644 ouid=root ogid=root rdev=00:00 obj=unconfined_u:object_r:user_tmp_t:s0 objtype=DELETE cap_fp=none cap_fi=none cap_fe=0 cap_fver=0
node=web type=PATH msg=audit(09/19/2020 17:15:57.718:1621) : item=1 name=/etc/nginx/ inode=80 dev=08:01 mode=dir,755 ouid=root ogid=root rdev=00:00 obj=system_u:object_r:httpd_config_t:s0 objtype=PARENT cap_fp=none cap_fi=none cap_fe=0 cap_fver=0
node=web type=PATH msg=audit(09/19/2020 17:15:57.718:1621) : item=0 name=/etc/nginx/ inode=80 dev=08:01 mode=dir,755 ouid=root ogid=root rdev=00:00 obj=system_u:object_r:httpd_config_t:s0 objtype=PARENT cap_fp=none cap_fi=none cap_fe=0 cap_fver=0
node=web type=CONFIG_CHANGE msg=audit(09/19/2020 17:15:57.718:1622) : auid=vagrant ses=5 op=updated_rules path=/etc/nginx/nginx.conf key=web_config_changed list=exit res=yes
node=web type=PROCTITLE msg=audit(09/19/2020 17:15:57.718:1623) : proctitle=vi /etc/nginx/nginx.conf
node=web type=PATH msg=audit(09/19/2020 17:15:57.718:1623) : item=1 name=/etc/nginx/nginx.conf inode=4998274 dev=08:01 mode=file,644 ouid=root ogid=root rdev=00:00 obj=unconfined_u:object_r:httpd_config_t:s0 objtype=CREATE cap_fp=none cap_fi=none cap_fe=0 cap_fver=0
node=web type=PATH msg=audit(09/19/2020 17:15:57.718:1623) : item=0 name=/etc/nginx/ inode=80 dev=08:01 mode=dir,755 ouid=root ogid=root rdev=00:00 obj=system_u:object_r:httpd_config_t:s0 objtype=PARENT cap_fp=none cap_fi=none cap_fe=0 cap_fver=0
node=web type=PROCTITLE msg=audit(09/19/2020 17:15:57.723:1624) : proctitle=vi /etc/nginx/nginx.conf
node=web type=PATH msg=audit(09/19/2020 17:15:57.723:1624) : item=0 name=/etc/nginx/nginx.conf inode=4998274 dev=08:01 mode=file,644 ouid=root ogid=root rdev=00:00 obj=unconfined_u:object_r:httpd_config_t:s0 objtype=NORMAL cap_fp=none cap_fi=none cap_fe=0 cap_fver=0
node=web type=PROCTITLE msg=audit(09/19/2020 17:15:57.723:1625) : proctitle=vi /etc/nginx/nginx.conf
node=web type=PATH msg=audit(09/19/2020 17:15:57.723:1625) : item=0 name=/etc/nginx/nginx.conf inode=4998274 dev=08:01 mode=file,644 ouid=root ogid=root rdev=00:00 obj=unconfined_u:object_r:user_tmp_t:s0 objtype=NORMAL cap_fp=none cap_fi=none cap_fe=0 cap_fver=0
node=web type=PROCTITLE msg=audit(09/19/2020 17:15:57.723:1626) : proctitle=vi /etc/nginx/nginx.conf
node=web type=PATH msg=audit(09/19/2020 17:15:57.723:1626) : item=0 name=/etc/nginx/nginx.conf inode=4998274 dev=08:01 mode=file,644 ouid=root ogid=root rdev=00:00 obj=unconfined_u:object_r:user_tmp_t:s0 objtype=NORMAL cap_fp=none cap_fi=none cap_fe=0 cap_fver=0
type=USER_CMD msg=audit(09/19/2020 17:19:38.203:1598) : pid=4295 uid=vagrant auid=vagrant ses=8 subj=unconfined_u:unconfined_r:unconfined_t:s0-s0:c0.c1023 msg='cwd=/mnt/logging/192.168.0.220 cmd=cat nginx_access.log terminal=pts/0 res=success'
```
