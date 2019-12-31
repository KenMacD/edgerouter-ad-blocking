## Purpose

These instructions are the steps I followed to set up ad blocking on a Ubiquiti EdgeRouter using https://github.com/notracking/hosts-blocklists.


## Prerequisites

 * DNS forwarding should already be set up and working on the interfaces where you want ad blocking.
 * SSH should be enabled

## Steps

#### Download `notracking`

Connect to the EdgeRouter and become root:

```
$ ssh <router>
$ sudo -s
```

On the EdgeRouter download the `notracking_update` script with:

```
# curl https://raw.githubusercontent.com/notracking/hosts-blocklists-scripts/67b4ae832771c888feb85db1fc257bbb5d52f739/notracking_update | sed "s^/path/to/lists/directory^/config/user-data/notracking/^" >/config/scripts/notracking_update
# chmod 755 /config/scripts/notracking_update
# mkdir /config/user-data/notracking
```

Try the script to make sure that it creates files in `/config/user-data/notracking`.

```
# /config/scripts/notracking_update
```

#### Set up a cron to update

Set the script to run once a day. This uses 2am, but can be changed.

```
set system task-scheduler task notracking_update crontab-spec "0 2 * * *"
set system task-scheduler task notracking_update executable path /config/scripts/notracking_update
```

#### Add to dns forwarding (dnsmasq) options

```
set service dns forwarding options addn-hosts=/config/user-data/notracking/hostnames.txt
set service dns forwarding options conf-file=/config/user-data/notracking/hostnames.txt
```
