### selinux ###

selinux is integrated to the kernel as a module

# The differents selinux modes: strict, permissive, disable

* strict: Enforcing
* permissive: Permissive
* disable: Disable 

# get / set Temporary Mode  

```bash
$ getenforce
Permissive
$ sudo setenforce 0
$ getenforce
Permissive
$ sudo setenforce 1
$ getenforce
Enforcing
```
# Default config file: /etc/selinux/config

```bash
cat /etc/selinux/config | grep -v '#'
# SELINUX= can take one of these three values:
#     enforcing - SELinux security policy is enforced.
#     permissive - SELinux prints warnings instead of enforcing.
#     disabled - No SELinux policy is loaded.
SELINUX=enforcing
# SELINUXTYPE= can take one of three values:
#     targeted - Targeted processes are protected,
#     minimum - Modification of targeted policy. Only selected processes are protected.
#     mls - Multi Level Security protection.
SELINUXTYPE=targeted
```

## Add a port
```
semanage port -l|grep 8090
sudo semanage port -a -t http_port_t -p tcp 8090
```

# get  ps Z
```bash
ps axfZ
```

# utilisateur:rôle:type:niveau
U:R:T:L
```bash
ls -Z
-rw-r--r--. root root unconfined_u:object_r:user_home_t:s0 DESCRIPTION.rst
-rw-r--r--. root root unconfined_u:object_r:user_home_t:s0 setup.cfg
```

# matchpathcon can be use to get the selinux attributs
```bash
matchpathcon /var/ww
/var/ww	system_u:object_r:var_t:s0
```

# Troubleshouting: sealert
```bash
yum install setroubleshoot-server

sudo sealert -a /var/log/audit/audit.log | less
```

# Update a context: matchpatchcon
```bash
$ matchpathcon /var/www/html
/var/www/html	system_u:object_r:httpd_sys_content_t:s0
$ matchpathcon testsite
testsite	unconfined_u:object_r:user_home_t:s0
$ sudo semanage fcontext -a -t httpd_sys_content_t 'testsite/(.*)?'
[vagrant@promotheus1 git]$ matchpathcon testsite
```

# Booleans: getsebool & setsebool (>100)

* getsebool -a | grep http
* setsebool -P httpd_enable_homedirs on    (-P: permanent)

```bash
$ sudo sealert -a /var/log/audit/audit.log
*****  Plugin catchall_boolean (32.5 confidence) suggests   *****
If you want to allow httpd to enable homedirs
Then you must tell SELinux about this by enabling the 
'httpd_enable_homedirs' boolean. Do
setsebool -P httpd_enable_homedirs 1
```

```bash
$ getsebool -a|grep http|grep dir
httpd_dontaudit_search_dirs --> off
httpd_enable_homedirs --> off
$ sudo setsebool -P httpd_enable_homedirs on
$ getsebool httpd_enable_homedirs
httpd_enable_homedirs --> on
```


