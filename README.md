# scaleio-volume-backup

### A set of scripts for creating ScaleIO snapshots, dumping them to an encrypted file, and restoring such files.

The scripts will hunt through a list of servers to find the current primary MDM, on which the "scli" command will be executed.

## Requirements:

1. These scripts must be executed from a host that can "ssh" to all ScaleIO MDM hosts as root, without password. You are encouraged to modify these scripts to suit your security needs within your environment.
2. SDC client must be present on the machine running the "dump_snapshot" or "restore_snapshot" commands, as volumes will be mounted to this machine during the process.
3. The "openssl" package needs to be present on the host doing the dumping or restoring.

## Installation

mkdir /root/bin

mv -t /root/bin snapshot_volume dump_snapshot restore_snapshot

mv scaleio-backup-password.txt /etc

You will need to edit all 3 scripts to change the hostnames of your scaleIO MDMs, passwords, IP address of the SDC host running these scripts, etc.

__**Be sure to change the password in /etc/scaleio-backup-password.txt to something unique! You could optionally modify the scripts to instead use "-pass env:ENVVAR" rather than storing the password in a file.**__

## Usage


### snapshot_volume

 This script will snapshot a given VOLUME_NAME, first deleting the oldest snapshot if 31 snapshots already exist.

 Usage: snapshot_volume (volume_name)


### dump_snapshot

 This script will temporarily attach a given snapshot VOLUME_ID, and encrypt it into a gzip file.

 Usage: dump_snapshot (volume_id)


### restore_snapshot

 This script will create a new ScaleIO volume of the specified size and name and restore an encrypted gzip volume dump into it.

 Usage: restore_snapshot (new_volume_name) (size_in_gb) (encrypted_gz_filename)

 Example: restore_snapshot testvol2 16 /volumedump/596fbe910000001b.gz
