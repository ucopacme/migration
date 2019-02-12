Test Cases for OS Validation: Linux
===================================

DNS configuration
-----------------

verify DNS is configured for pre-cutover::

  nslookup new-<hostname>.ldc.devops.ucop.edu
  nslookup old-<hostname>.ldc.devops.ucop.edu
  nslookup <hostname>.ldc.devops.ucop.edu
  nslookup <hostname>.ucop.edu


ssh access
----------

verify host is accessible over ssh::

  ssh <my_user_name>@old-<hostname>
  ssh <my_user_name>@new-<hostname>


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


resolver
--------

verify DNS resolver is configured correctly::

  cat /etc/resolv.conf

verify host resolves names in various domains:

- ldc::

  host unxpupp02.ldc.devops.ucop.edu


- on-prem::

  host time.ucop.edu

- external::

  host ftp.suse.de


hostname
--------

verify fully quilified domain and short hostname are set properly::

  hostname -f
  hostname


base firewall rules
-------------------

verify iptables rules on host::

  sudo iptables -L -vn


AD access (centrify)
--------------------

verify host is connected to it's centify zone::

  adinfo


puppet
------

verify puppet is configurd and running properly::

  sudo tail -2 /var/log/puppet/puppet.log
  sudo puppet agent -t --noop


remote logging
--------------

verify logs are syncing to central log host::

  sudo grep -v ^# /etc/rsyslog.d/remote.conf
  sudo netstat -taupn | grep syslog


backups
-------

verify nightly backups are configured and running::

  sudo service avagent status
  sudo tail /var/avamar/avagent.log


monitoring
----------

verify monitoring agent is communicating with servers::

  ps aux| grep nimbus
  tail /apps/CA/uim/robot/controller.log

