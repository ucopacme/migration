DNS Configuration for Migration
===============================

Goals
-----

- allow easy cutover and failback 
- allow access to both source and target hosts at all stages of migration process
- maintain a record of source and target IP addresses
- dns configuration for migration is scriptable


Preparation
-----------

ldc.devops.ucop.edu (Route53 Hostedzone)
****************************************

create 2 A records:

- old-<hostname>.ldc.devop.ucop.edu set to IP address in SDSC
- new-<hostname>.ldc.devop.ucop.edu set to IP address in AWS VPC

create 1 CNAME record:

- <hostname>.ldc.devop.ucop.edu is pointer to old-<hostname>.ldc.devop.ucop.edu


ucop.edu (Infoblox)
*******************

- delete existing A record for <hostname>.ucop.edu
- recreate <hostname>.ucop.edu as CNAME record pointing to <hostname>.ldc.devop.ucop.edu


Example
*******

::

  agould@horus:~> host new-opsmond01
  new-opsmond01.ldc.devops.ucop.edu has address 10.48.76.164
  
  agould@horus:~> host old-opsmond01
  old-opsmond01.ldc.devops.ucop.edu has address 128.48.76.164
  
  agould@horus:~> host opsmond01.ldc.devops.ucop.edu
  opsmond01.ldc.devops.ucop.edu is an alias for new-opsmond01.ldc.devops.ucop.edu.
  new-opsmond01.ldc.devops.ucop.edu has address 10.48.76.164
  
  agould@horus:~> host opsmond01.ucop.edu
  opsmond01.ucop.edu is an alias for opsmond01.ldc.devops.ucop.edu.
  opsmond01.ldc.devops.ucop.edu is an alias for new-opsmond01.ldc.devops.ucop.edu.
  new-opsmond01.ldc.devops.ucop.edu has address 10.48.76.164


Cutover
-------

In ldc.devops.ucop.edu Route53 Hostedzone:

- change CNAME record <hostname>.ldc.devop.ucop.edu to point to new-<hostname>.ldc.devop.ucop.edu

Failback
--------

In ldc.devops.ucop.edu Route53 Hostedzone:

- change CNAME record <hostname>.ldc.devop.ucop.edu to point to old-<hostname>.ldc.devop.ucop.edu


Post-Migration
--------------

After source host is fully decommissioned make the following changes in ldc.devops.ucop.edu Route53 Hostedzone to cleanup obsolete dns records:

- delete CNAME record <hostname>.ldc.devop.ucop.edu
- recreate <hostname>.ldc.devop.ucop.edu as A record set to IP address in AWS VPC
- delete A record new-<hostname>.ldc.devop.ucop.edu
- delete A record old-<hostname>.ldc.devop.ucop.edu



