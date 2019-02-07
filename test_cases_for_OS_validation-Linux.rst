Test Cases for OS Validation: Linux
===================================

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


domainname resolution
---------------------

verify DNS resolver is configured correctly

verify host resolves names in various domains:

- ldc::

  host unxpupp02.ucop.edu


- on-prem::

  host time.ucop.edu

- external::

  host ftp.suse.de


hostname
--------

verify fully quilified domain name matches DNS A record::

  hostname -f
  dig $(hostname -f)

verify short hostname set properly::

  hostname


base firewall rules
-------------------

verify iptables rules on host::

  sudo iptables -L -vn


ssh access
----------

verify host is accessible over ssh::

  ssh <my_user_name>@<hostname>


AD access (centrify)
--------------------

verify host is connected to it's centify zone::

  adinfo


puppet
------

verify puppet is configurd and running properly::

  sudo tail /var/log/puppet/puppet.log
  sudo puppet agent -t --noop


remote logging
--------------

verify logs are syncing to central log host::

  ? 

backups
-------

verify nightly backups are configured and running::

  sudo service avagent status
  sudo tail /var/avamar/avagent.log


monitoring
----------

verify monitoring agent is communicating with servers::

  sudo service nimbus status
  tail /apps/CA/uim/robot/controller.log

