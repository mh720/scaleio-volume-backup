# scaleio-volume-backup

### A set of scripts for creating ScaleIO snapshots, dumping them to an encrypted file, and restoring such files.

The scripts will hunt through a list of ScaleIO MDM servers to find the current primary, on which "scli" commands will be executed to attach, detach, and create new snapshots and volumes.

## Requirements:

1. The scripts are written to be executed from a host that can "ssh" to all ScaleIO MDM hosts in your cluster as root, without password. You are encouraged to modify these scripts to suit your security needs within your environment.

2. SDC client must be present on the machine running the "dump_snapshot" or "restore_snapshot" commands, as volumes will be mounted to this machine during the process.

3. The scripts expect that the defined TARGET_DEVICE (ex: /dev/scinia) will be the device name used when dumping or restoring. For this reason, you are encouraged to use a host that doesn't dynamically mount ScaleIO volumes for other purposes.

4. The "openssl" and "pv" packages needs to be present on the host doing the dumping or restoring. If rate-limiting isn't desired, you can remove the inline 'pv' commands from the dump and restore scripts.

## Installation

mkdir /root/bin

mv -t /root/bin snapshot_volume dump_snapshot restore_snapshot

mv backup-password.txt /etc

You will need to edit each script with the hostname(s) of your scaleIO MDM(s) and scli password, and path to dump or restore files to/from.

__**Be sure to change the password in /etc/backup-password.txt to something unique! You could optionally modify the scripts to instead use "-pass env:ENVVAR" rather than storing the password in a file.**__

## Usage


### snapshot_volume

This script will snapshot a given volume name, first deleting the oldest snapshot if 31 snapshots already exist.

Usage:  snapshot_volume [OPTIONS] (-n volume_name)

OPTIONS:

 -h, -?   help

 -i       display only then new shapshot_ID upon success

 -v       increase verbosity


### dump_snapshot

This script will attach a given snapshot volume ID temporarily as (ex: /dev/scinia) and encrypt it into a gzip file within (ex: /volumedump/).

Usage:  dump_snapshot [OPTIONS] (-i volume_id)

OPTIONS:

 -h, -?   help

 -f       display only the output filepath upon success

 -v       increase verbosity


### restore_snapshot

This script will create a new ScaleIO volume of the specified name and size, temporarily attach it to (ex: /dev/scinia) on this host, and restore an encrypted gzip volume dump into it.

Usage: restore_snapshot [OPTIONS] (-n new_volume_name) (-s size_in_gb) (-f encrypted_gz_filename)

Example: restore_snapshot -n testvol2 -s 16 -f /volumedump/596fbe910000001b.gz

OPTIONS:

 -h, -?   help

 -f       filepath of encrypted gzip file

 -n       name of the new scaleIO volume to create

 -s       size of the new volume to create in GB (should match the original dumped volume size)

 -v       increase verbosity
