# Install check_mk agent

### On agent server

```yum install xinetd```

```yum install check-mk-agent```

###
Open 6556 in firewall:
Vim /etc/sysconfig/iptables (copy like 22 port)


### On web install check_mk_server
Go to `WATO - configuration`
-> `host` -> add new host
Remember `Apply change`
