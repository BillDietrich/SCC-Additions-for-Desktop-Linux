# README for using SysConfCollect (SCC) on a desktop Linux system

SysConfCollect (SCC) checks for changes in files and config settings and much more in your system.

[SysConfCollect](https://sourceforge.net/projects/sysconfcollect/ "SysConfCollect on SourceForge")

[SysConfCollect home](http://sysconfcollect.sourceforge.net/ "SysConfCollect home site")

## Installation

I installed SCC client 1.25.35 in 8/2019 and tested on a Linux Mint 19.2 Cinnamon desktop system.

Do client-only install ("scc").  I installed from deb file under
[SysConfCollect scc files](https://sourceforge.net/projects/sysconfcollect/files/scc/ "SysConfCollect scc files"),
and it did a snapshot and produced HTML files.  Then I did:
```bash
sudo chmod -R a+r /var/opt/scc/data
# then in GUI file explorer open /var/opt/scc/data/index.html
# or in browser open file:///var/opt/scc/data/index.html
```

## Added modules for desktop Linux

I created three new modules:

* scc_0501_u_network_linuxdesktop

* scc_0601_u_software_linuxdesktop

* scc_1001_s_users_linuxdesktop

## Taking more snapshots

To do another snapshot (takes about 1 minute):
```bash
sudo /opt/scc/bin/scc
sudo chmod -R a+r /var/opt/scc/data
```

To add to cron, to do a snapshot as root every time the system boots:
```bash
sudo crontab -e
# add one line such as:
@reboot /opt/scc/bin/scc
@reboot /opt/scc/bin/scc --extra linuxdesktop
30 11 * * * /opt/scc/bin/scc --extra linuxdesktop --max_age 1
# --extra MODPATTERN  will run any additional modules named *MODPATTERN*
# --max_age 1  will delete any log entries more than 1 month old
```

So normal-user browser can access data via bookmark to "file:///var/opt/scc/data/index.html":
```bash
sudo xed /opt/scc/bin/scc
# add just before "exit" line at end of file:
chmod -R a+r "${SCC_DATA}"
# a bit dangerous: any user will be able to read some sensitive data in scc output
```

## Options

```bash
sudo /opt/scc/bin/scc --help
ls /opt/scc/bin/scc_modules		# see all modules

sudo /opt/scc/bin/scc --extra MODXXX		# run modules named *MODXXX* in addition to usual modules

# Some limited configuration changes can be made by copying
# /etc/opt/scc/newconfig/scc-localize
# to
# /etc/opt/scc/conf/scc-localize
# and then editing it.

# If you want to disable parts of a standard module, you'd
# have to comment out parts of that module's shell script
# in /opt/scc/bin/scc_modules

# To create a new module, either:
# copy /opt/scc/bin/scc-plugin to /opt/scc/bin/scc_modules/scc_NNN_s_NEWMODNAME
# or:
# copy some existing module in /opt/scc/bin/scc_modules to a new name
# and edit it to make your new module
# have to add a --extra argument to get it to run

# general form of code in new module:
echo 'fix:network1::this is static text only'
echo 'var:network1::static text followed by varying data ' `${some_bin} status | grep 'IP:'`
```

## Quirks

* SCC really is intended for a server system which rarely/never reboots.  Rebooting your desktop Linux system, which you probably do daily, changes all kinds of things such as IRQ assignments.  So you will get lots of reports that those have changed.  Ignore them.

* SCC really is intended for a server system which rarely/never updates itself.  Updating your desktop Linux system, which you probably do daily or weekly, changes all kinds of things such as kernel version, specific app packages, etc.  So you will get lots of reports that those have changed.  Check them and compare to the updates you requested.


