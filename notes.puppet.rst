edit /etc/resolve.conf
----------------------

ec2 uses DHCP to generate /etc/resolve.conf poining to the VPC default 
name service.  this brakes puppet, because it can not resolve the puppet
servers' hostnames.

I think DHCP rewrites it every reboot

but wait - if we edit this file we can supply the values dhclient uses to 
populate resolve.conf::

  agould@aigtest04:~> cat /etc/dhclient-eth0.conf
  supersede domain-name "ucop.edu";
  supersede domain-name-servers 128.48.89.70,128.48.185.70;



create a hiera node file
------------------------

delete lvm stuff from hiera node files
ec2 instances do not use the same disk device names as SDCS VMs


reset puppet cert
-----------------

this is normal for a new puppet node


puppet infrastructure issues
----------------------------

can not ssl when server is unxpupp01.  effects on-prem also.  I think the
server cert is expired.

hostname for puppetdb server is wrong::

  agould@unxpdbp02:~> hostname -f
  unxpdbp02.ucop.edu
  agould@unxpdbp02:~> host unxpdbp02.ucop.edu
  Host unxpdbp02.ucop.edu not found: 3(NXDOMAIN)
  agould@unxpdbp02:~> ip add
  1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN
      link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
      inet 127.0.0.1/8 scope host lo
      inet6 ::1/128 scope host
         valid_lft forever preferred_lft forever
  2: eth0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 9001 qdisc pfifo_fast state UP qlen 1000
      link/ether 02:33:03:3d:c1:f6 brd ff:ff:ff:ff:ff:ff
      inet 10.48.68.115/24 brd 10.48.68.255 scope global eth0
      inet6 fe80::33:3ff:fe3d:c1f6/64 scope link
         valid_lft forever preferred_lft forever
  agould@unxpdbp02:~> host 10.48.68.115
  Host 115.68.48.10.in-addr.arpa. not found: 3(NXDOMAIN)
  agould@unxpdbp02:~> host unxpdbp01
  unxpdbp01.ucop.edu is an alias for unxpdbp01.ldc.devops.ucop.edu.
  unxpdbp01.ldc.devops.ucop.edu has address 10.48.68.115





centrify issues
---------------

::

  agould@opsmond01:~> adinfo
  Local host name:   opsmond01
  Joined to domain:  ad.ucop.edu
  Joined as:         opsmond01.ad.ucop.edu
  Pre-win2K name:    opsmond01
  Current DC:        p-irc-dc06.ad.ucop.edu
  Preferred site:    SDSC
  Subnet site:
    Warning! Unable to locate computer's subnet site in Active Directory.
    Please advise your system administrator.
  Zone:              ad.ucop.edu/Unix/Zones/drtools
  CentrifyDC mode:   connected
  Licensed Features: Enabled


