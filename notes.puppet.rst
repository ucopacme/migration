edit /etc/resolve.conf
----------------------

ec2 uses DHCP to generate /etc/resolve.conf poining to the VPC default 
name service.  this brakes puppet, because it can not resolve the puppet
servers' hostnames.

I think DHCP rewrites it every reboot


create a hiera node file
------------------------

delete lvm stuff from hiera node files
ec2 instances do not use the same disk device names as SDCS VMs


reset puppet cert
-----------------

this is normal for a new puppet node


issues
------

can not ssl when server is unxpupp01.  effects on-prem also



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


