---
layout: default
title: Known Issues
sorting: 50
published: true
tags: [overviews, releases, latest release, "3.8", platforms, versions, known issues]
---

CFEngine defects are managed in our [bug tracker][bug tracker]. Please report
bugs or unexpected behavior there, following the documented guideline for new
bug reports.

The items below highlight issues that require additional awareness when starting
with CFEngine or when upgrading from a previous version.

### Error installing hub package

When installing the hub package there is an error for Failed dependencies.

```console
root@hub vagrant]# rpm -i /vagrant/cfengine-nova-hub-3.9.0b1-1.x86_64.rpm
error: Failed dependencies:
    libtool-ltdl is needed by cfengine-nova-hub-3.9.0b1-1.x86_64
```

The =libtool-ltdl= package must be installed first.

For example:

```console
yum -y install libtool-ltdl
```

### HP-UX specific

* [Package promises][packages] do not have out-of-the-box support for the HP-UX
  specific package manager. The workaround is to call the package manager
  directly using [commands promises][commands].
* Some important system information is missing from the HP-UX inventory report,
  as well as from CFEngine hard classes and system variables. The workaround is
  to use system tools to obtain the required information and set classes based
  on this.
        * Disk free
        * Memory size
        * Several OS and architecture specific attributes
                * System version
                * System serial number
                * System manufacturer
                * CPU model
                * BIOS version
                * BIOS vendor
* [Process promises][processes] depend on the `ps` native tool, which by
  default truncates lines at 128 columns on HP-UX. It is recommended to edit
  the file `/etc/default/ps` and increase the `DEFAULT_CMD_LINE_WIDTH` setting
  to 1024 to guarantee that process promises will work smoothly on the
  platform.
* Upgrading CFEngine on HP-UX is not supported by the out-of-the-box policy.
  There is a [support article](https://cfengine.zendesk.com/hc/en-us/articles/205454908)
  with a workaround.

### Enterprise emails sent for alert noticies come from 'admin@organization.com'.
There is currently no setting in Mission Portal to configure the sender email
address. This issue is on the [backlog](https://dev.cfengine.com/issues/6726)
and will be addressed in a future release.

To change the setting you must edit the from email address in
`/var/cfengine/httpd/htdocs/application/config/appsettings.php`

```
// Default FROM email address
$config['appemail'] = 'admin@organisation.com';
```

### Enterprise monitoring graphs

Monitoring graphs are disabled by default in CFEngine Enterprise 3.6 and later
versions.  To enable them, change monitoring_include in
masterfiles/controls/VERSION/reports.cf to e.g. ".*".  Note that this has a
significant impact on the resource consumption of your hub.

Monitoring graphs are not supported on all platforms, currently AIX, HP-UX and
Windows do not have this data.

### Enterprise software inventory is not out-of-the-box

Software inventory is not out-of-the-box for reporting from the hub on other
platforms than Debian, Ubuntu and Red Hat/CentOS.

In order to add software inventory for other platforms,
please contact support for a custom policy.

### Enterprise - Unable to edit directory based users with dots in username

Mission Portal does not allow users from a directory to be edited if they have
dots in their username.

### Enterprise Hub - PHP warnings after upgrading from `3.6.x`

After upgrading from `3.6.x` PHP warns it is unable to
initialize the apc module.

```
  notice: Q: "...hp/bin/php /var": PHP Warning:  PHP Startup: apc: Unable to initialize module
Q: "...hp/bin/php /var": Module compiled with module API=20100525
Q: "...hp/bin/php /var": PHP    compiled with module API=20131226
Q: "...hp/bin/php /var": These options need to match
Q: "...hp/bin/php /var":  in Unknown on line 0
```

This warning can be resolved by removing
`/var/cfengine/httpd/php/lib/apc.ini` and
`/var/cfengine/httpd/php/lib/php/extensions/no-debug-non-zts-20131226/apc.so`
