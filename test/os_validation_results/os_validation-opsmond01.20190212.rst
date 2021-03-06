Test Cases for OS Validation: Linux
===================================

DNS configuration
-----------------

verify DNS is configured for pre-cutover::

  nslookup <hostname>
  nslookup <hostname>.ucop.edu
  nslookup <hostname>.ldc.devops.ucop.edu

results::

  agould@horus:~> nslookup opsmond01.ucop.edu
  Server:		128.48.249.130
  Address:	128.48.249.130#53
  
  opsmond01.ucop.edu	canonical name = opsmond01.ldc.devops.ucop.edu.
  opsmond01.ldc.devops.ucop.edu	canonical name = old-opsmond01.ldc.devops.ucop.edu.
  Name:	old-opsmond01.ldc.devops.ucop.edu
  Address: 128.48.76.164
  
  agould@horus:~> nslookup opsmond01.ldc.devops.ucop.edu
  Server:		128.48.249.130
  Address:	128.48.249.130#53
  
  Non-authoritative answer:
  opsmond01.ldc.devops.ucop.edu	canonical name = old-opsmond01.ldc.devops.ucop.edu.
  Name:	old-opsmond01.ldc.devops.ucop.edu
  Address: 128.48.76.164
  
  agould@horus:~> nslookup new-opsmond01.ldc.devops.ucop.edu
  Server:		128.48.249.130
  Address:	128.48.249.130#53
  
  Non-authoritative answer:
  Name:	new-opsmond01.ldc.devops.ucop.edu
  Address: 10.48.76.164
  
  agould@horus:~> nslookup old-opsmond01.ldc.devops.ucop.edu
  Server:		128.48.249.130
  Address:	128.48.249.130#53
  
  Non-authoritative answer:
  Name:	old-opsmond01.ldc.devops.ucop.edu
  Address: 128.48.76.164



ssh access
----------

verify host is accessible over ssh::

  ssh <my_user_name>@old-<hostname>
  ssh <my_user_name>@new-<hostname>

results::

  agould@horus:~> ssh old-opsmond01
  Last login: Fri Feb  8 14:45:29 2019 from horus.ucop.edu
  agould@opsmond01:~> logout
  Connection to old-opsmond01 closed.

  agould@horus:~> ssh new-opsmond01
  Warning: Permanently added 'new-opsmond01' (RSA) to the list of known hosts.
  Last login: Thu Feb  7 11:13:14 2019 from horus.ucop.edu


reboot
------

verify host is accessible after reboot::

  sudo reboot


network connectivity
--------------------

verify network configuration and routing::

  cat /etc/sysconfig/network-scripts/ifcfg-eth0
  ip add
  ip route
  sudo service network restart
  ip add
  ip route

results::

  agould@opsmond01:~>   cat /etc/sysconfig/network-scripts/ifcfg-eth0
  # Automatically generated by the vm import process
  DEVICE=eth0
  ONBOOT=yes
  BOOTPROTO=dhcp
  TYPE=Ethernet
  NM_CONTROLLED=no
  IPV6INIT=no
  
  agould@opsmond01:~>   ip add
  1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN 
      link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
      inet 127.0.0.1/8 scope host lo
      inet6 ::1/128 scope host 
         valid_lft forever preferred_lft forever
  2: eth0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 9001 qdisc pfifo_fast state UP qlen 1000
      link/ether 02:f3:3d:e9:60:26 brd ff:ff:ff:ff:ff:ff
      inet 10.48.76.164/23 brd 10.48.77.255 scope global eth0
      inet6 fe80::f3:3dff:fee9:6026/64 scope link 
         valid_lft forever preferred_lft forever
  agould@opsmond01:~>   ip route
  10.48.76.0/23 dev eth0  proto kernel  scope link  src 10.48.76.164 
  169.254.0.0/16 dev eth0  scope link  metric 1002 
  default via 10.48.76.1 dev eth0 


resolver
--------

verify DNS resolver is configured correctly::

  cat /etc/resolv.conf

results::

  agould@opsmond01:~> cat /etc/resolv.conf
  ## /etc/resolv.conf
  ## Do Not Edit - managed by puppet
  
  domain ucop.edu
  search ucop.edu ad.ucop.edu cdlib.org ist.berkeley.edu
  nameserver 128.48.89.70
  nameserver 128.48.185.70
  nameserver 128.48.249.130


verify host resolves names in various domains:

- ldc::

  host unxpupp02.ldc.devops.ucop.edu

- on-prem::

  host time.ucop.edu

- external::

  host ftp.suse.de

results::

  agould@opsmond01:~> host unxpupp02.ldc.devops.ucop.edu
  unxpupp02.ldc.devops.ucop.edu has address 10.48.68.116
  agould@opsmond01:~> host time.ucop.edu
  time.ucop.edu has address 128.48.102.70
  agould@opsmond01:~> host ftp.sudo.de
  ftp.sudo.de has address 217.160.231.170


hostname
--------

verify fully quilified domain and short hostname are set properly::

  hostname -f
  hostname

results::

  agould@opsmond01:~> hostname -f
  opsmond01.ldc.devops.ucop.edu
  agould@opsmond01:~> hostname
  opsmond01.ldc.devops.ucop.edu

**FAIL** short hostname query returns fqdn


base firewall rules
-------------------

verify iptables rules on host::

  sudo iptables -L -vn

results::

  agould@opsmond01:~> sudo iptables -L -vn
  Chain INPUT (policy ACCEPT 0 packets, 0 bytes)
   pkts bytes target     prot opt in     out     source               destination
    8   588 ACCEPT     icmp --  *      *       0.0.0.0/0            0.0.0.0/0           /* 000 accept all icmp */
    0     0 ACCEPT     all  --  lo     *       0.0.0.0/0            0.0.0.0/0           /* 001 accept all to lo interface */
  1252  194K ACCEPT     all  --  *      *       0.0.0.0/0            0.0.0.0/0           /* 002 accept related established rules */ state RELATED,ESTABLISHED
    1    60 ACCEPT     tcp  --  *      *       0.0.0.0/0            0.0.0.0/0           multiport dports 22 /* 003 accept all ssh */ state NEW
    0     0 ACCEPT     icmp --  *      *       128.48.0.0/16        0.0.0.0/0           /* 1 - All Ucop */
    0     0 ACCEPT     tcp  --  *      *       128.48.0.0/16        0.0.0.0/0           multiport dports 8080,8081,5001 /* 110 - class ca::apm::server::firewall */ state NEW
    0     0 ACCEPT     tcp  --  *      *       128.48.119.0/24      0.0.0.0/0           multiport dports 48000:48030 /* 110 - class ca::uim::robot::firewall */ state NEW
    0     0 ACCEPT     tcp  --  *      *       128.48.64.0/19       0.0.0.0/0           multiport dports 48000:48030 /* 111 - class ca::uim::robot::firewall */ state NEW
    2   120 ACCEPT     tcp  --  *      *       10.48.64.0/19        0.0.0.0/0           multiport dports 48000:48030 /* 112 - class ca::uim::robot::firewall */ state NEW
    0     0 ACCEPT     tcp  --  *      *       52.39.140.103        0.0.0.0/0           multiport dports 48000:48099 /* 200 - TCP access from AWS */ state NEW
    0     0 ACCEPT     udp  --  *      *       52.39.140.103        0.0.0.0/0           multiport dports 48000:48099 /* 300 - UDP access from AWS */ state NEW
    8   920 ACCEPT     udp  --  *      *       128.48.0.0/16        0.0.0.0/0           /* 5052 - All Ucop */
   88 34321 ACCEPT     tcp  --  *      *       128.48.0.0/16        0.0.0.0/0           /* 5053 - All Ucop */
    9   456 DROP       all  --  *      *       0.0.0.0/0            0.0.0.0/0           /* 999 drop all */


AD access (centrify)
--------------------

verify host is connected to it's centify zone::

  adinfo

results::

  agould@opsmond01:~> adinfo
  Local host name:   opsmond01
  Joined to domain:  ad.ucop.edu
  Joined as:         opsmond01.ad.ucop.edu
  Pre-win2K name:    opsmond01
  Current DC:        p-irc-dc04.ad.ucop.edu
  Preferred site:    SDSC
  Subnet site:
    Warning! Unable to locate computer's subnet site in Active Directory.
    Please advise your system administrator.
  Zone:              ad.ucop.edu/Unix/Zones/drtools
  CentrifyDC mode:   connected
  Licensed Features: Enabled

**WARN** - subnet site not in AD


puppet
------

verify puppet is configurd and running properly::

  sudo tail -2 /var/log/puppet/puppet.log
  sudo puppet agent -t --noop

results::

  agould@opsmond01:~>   sudo tail -2 /var/log/puppet/puppet.log
  Tue Feb 12 10:37:54 -0800 2019 /Stage[main]/Puplab_fw::Post/Firewall[999 drop all]/ensure (notice): created
  Tue Feb 12 10:37:58 -0800 2019 Puppet (notice): Finished catalog run in 18.24 seconds
  agould@opsmond01:~>   sudo puppet agent -t --noop
  Info: Retrieving pluginfacts
  Info: Retrieving plugin
  Info: Loading facts
  Info: Loading facts
  Info: Caching catalog for opsmond01.ldc.devops.ucop.edu
  Info: Applying configuration version '1549994190'
  Notice: Finished catalog run in 8.49 seconds


remote logging
--------------

verify logs are syncing to central log host::

  sudo grep -v ^# /etc/rsyslog.d/remote.conf
  sudo netstat -taupn | grep syslog

results::

  agould@opsmond01:~>   sudo grep -v ^# /etc/rsyslog.d/remote.conf
  
  $WorkDirectory /var/spool/rsyslog # where to place spool files
  $ActionQueueFileName uniqName # unique name prefix for spool files
  $ActionQueueMaxDiskSpace 1g   # 1gb space limit (use as much as possible)
  $ActionQueueSaveOnShutdown on # save messages to disk on shutdown
  $ActionQueueType LinkedList   # run asynchronously
  $ActionResumeRetryCount -1    # infinite retries if host is down
  *.* @@loghost.ucop.edu:5140
  authpriv.* @@sdsc-qrad-ha-vip.ucop.edu:514
  
  agould@opsmond01:~>   sudo netstat -taupn | grep syslog
  tcp        0      0 10.48.76.164:41484          128.48.68.177:514           ESTABLISHED 1163/rsyslogd
  tcp        0      0 10.48.76.164:45920          128.48.68.114:5140          ESTABLISHED 1163/rsyslogd


backups
-------

verify nightly backups are configured and running::

  sudo service avagent status
  sudo tail /var/avamar/avagent.log

results::

  agould@opsmond01:~>   sudo service avagent status
  avagent Info: Client Agent is running.
  avagent Info: Client activated with MCS "ava-sd-util.ucop.edu:28001"
  avagent Info: Client using DPN "ava-sd-util.ucop.edu"
  avagent Info: avagent script version 11
    version:     7.2.101-32
    build date:  Dec 20 2015 10:35:00
    msg format:  13-10
    SSL:         TLSv1   OpenSSL 1.0.1e-fips 11 Feb 2013
    Zlib:        1.2.3
    LZO:         1.08 Jul 12 2002
    platform:    Linux
    OS version:  SLES-64
    Processor:   x86_64
  
  agould@opsmond01:~>   sudo tail /var/avamar/avagent.log
  2019-02-12 11:09:57 avagent Info <6626>: Agent Main: Client ID (cid) = 8dfebd0584edf89efdc009526cc009d2199e263b
  2019-02-12 11:09:57 avagent Info <18918>: Registration: Processing secure registration with the MCS.
  2019-02-12 11:09:57 avagent Info <18921>: Registration: Requesting root CA from the MCS.
  2019-02-12 11:11:00 avagent Error <5365>: Cannot connect to 128.48.72.99:28001.
  2019-02-12 11:11:00 avagent Info <5059>: unable to connect, sleep(60) then retrying
  2019-02-12 11:13:03 avagent Error <5365>: Cannot connect to 128.48.72.99:28001.
  2019-02-12 11:13:03 avagent Info <5059>: unable to connect, sleep(60) then retrying
  2019-02-12 11:14:57 avagent Info <5705>: Updating IP address with the MCS. local IP='10.48.76.164', registered IP='(none)', port changed:false
  2019-02-12 11:15:06 avagent Error <5365>: Cannot connect to 128.48.72.99:28001.
  2019-02-12 11:15:06 avagent Info <5059>: unable to connect, sleep(60) then retrying

**FAIL** - unable to connect with backup server. probably not registered.

monitoring
----------

verify monitoring agent is communicating with servers::

  ps aux| grep nimbus
  tail /apps/CA/uim/robot/controller.log

result::

  agould@opsmond01:~> ps aux| grep nimbus
  agould    9925  0.0  0.0 103324   848 pts/0    S+   11:21   0:00 grep nimbus
  root     15237  0.0  0.0  12332   748 ?        Ss   Feb11   0:01 ./nimbus /data/uim
  root     15239  0.0  0.0  26276  3264 ?        S    Feb11   0:05 nimbus(controller)
  root     15240  0.0  0.0  90600  2060 ?        Sl   Feb11   0:03 nimbus(spooler)
  root     15242  0.0  0.0  15036  2180 ?        S    Feb11   0:01 nimbus(hdb)
  root     15244  0.0  0.0  93636  2612 ?        Sl   Feb11   0:05 nimbus(cdm)

  agould@opsmond01:~>   tail /apps/CA/uim/robot/controller.log
  Jul 29 21:07:48:701 [140198000252672] Controller:  Loglevel = 0, Logfile = controller.log
  Jul 29 21:07:48:704 [140198000252672] Controller:  Running as user root (0)
  Jul 29 21:07:48:704 [140198000252672] Controller: -----
  Jul 29 21:07:48:747 [140198000252672] Controller: Controller on opsmond01 port 48000 started
  Jul 29 21:07:48:869 [140198000252672] Controller: send_internal_alarm - failed to flush message (permission denied)
  Jul 29 21:07:49:924 [140198000252672] Controller: Hub T3_Dev1_Hub(128.48.119.29) contact established
  Jul 29 21:07:49:975 [140198000252672] Controller: Specified IP (128.48.97.102) is not recognized as a local ip. No IP change, using 128.48.74.37
  Aug  3 19:52:06:112 [140198000252672] Controller: failed to send alive to hub T3_Dev1_Hub(128.48.119.29) - communication error
  Aug 11 07:32:21:309 [140198000252672] Controller: Going down...
  Aug 11 07:32:21:357 [140198000252672] Controller: Down

**FAIL** - not connecting to UIM hub (or so it appears)
