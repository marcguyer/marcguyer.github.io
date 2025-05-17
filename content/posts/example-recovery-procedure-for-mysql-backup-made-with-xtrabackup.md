---
title: "Example Recovery Procedure for MySQL Backup Made with Xtrabackup"
date: 2019-09-24T17:09:56-04:00
draft: false
comments: true
toc: false
images:
tags:
  - mysql
  - xtrabackup
---

We have a pre-existing procedure for backing up a MySQL database using hot backups with the wonderful [Percona Xtrabackup](https://www.percona.com/doc/percona-xtrabackup/2.4/index.html) and in this case the deprecated [innobackupex](https://www.percona.com/doc/percona-xtrabackup/2.4/innobackupex/innobackupex_option_reference.html) binary.

Xtrabackup is nice since it creates a clean backup of InnoDB, XtraDB, and MyISAM tables quickly and _without locking_. There are countless useful features including point-in-time recovery, incremental backups, single table recovery, single partition recovery, I/O throttling, parallel processing, encryption, compression, streaming, and more.

**A basic recovery procedure document was needed so I wrote one. Here is is.**

The pre-existing backup procedure in play has a few quirks:

1. The deprecated `innobackupex` binary v2.4.15 is used to create the backups. `innobackupex` is a wrapper for `xtrabackup` with some additional features that were rolled into `xtrabackup` in later versions.
2. The non-default `--compress` and `--stream` options are used. This has the effect of creating a single compressed file with multiple compressed files within it.

Here's the procedure:

## Summary

1. [Retrieve](#1-retrieve-backups-from-s3-storage)
2. [Decompress](#2-decompress-backup-file)
3. [Prepare](#3-prepare-the-backup-files)
3. [Restore](#4-prepare-the-backup-files)

See also [Advanced Usage](#advanced-usage) for faster, more disk-efficient usage

## Procedure

### 1. Retrieve backups from S3 Storage

The current backup routine uses `innobackupex` to create full snapshot hot backups nightly and incremental backups every half hour during the day. These are all stored in an [Amazon S3](https://aws.amazon.com/s3/) bucket.

#### S3 Bucket Locations

In this particular instance, the complete snapshot backup files and the incremental backups are in S3. For example:

* Full Compressed Snapshots
    * s3://my-db-backup/xtrabackups/
* Incremental Backups
    * s3://my-db-backup/xtrabackups/incrs

#### Create a clean working directory and download the snapshot

Create a clean directory on the server where you will work with the backup files
```sh
mkdir /restore_tmp
```

Download the full snapshot:
```sh
aws s3 cp s3://my-db-backup/xtrabackups/mysqlbackup.qp.xbs /restore_tmp/
```

_NOTE: We're using the AWS CLI here. Authentication is accomplished with stored credentials for the current user. More about [AWS CLI Configuration](https://docs.aws.amazon.com/cli/latest/userguide/cli-chap-configure.html)._

### 2. Decompress Backup File

The following command decompresses the file into the same (current) directory
```sh
xbstream -x < mysqlbackup.qp.xbs
```

**There are still many compressed files in the result!** The next step is to decompress all compressed files in the current directory and can be done like so:
```sh
xtrabackup --decompress --remove-original --parallel=8 --target-dir=.
```

### 3. Prepare the Backup Files

Next we need to prepare the backup. This step completes any open transactions or rollbacks that happened during the original backup procedure.
```sh
xtrabackup --prepare --target-dir=.
```

### 4. Restore the files

The MySQL server must be shut down for this step.
```sh
systemctl stop mysql
```

The datadir must be empty for restoration. Consider backing up what's there if it's not empty.
```sh
rm -rf /var/lib/mysql/*
```

Now, move the backup into the datadir with the `xtrabackup` binary. Note that the `--target-dir` is required and is used to indicate where the files will be moved _from_:
```sh
xtrabackup --move-back --target-dir=.
```

The ownership must change:
```sh
chown -R mysql:mysql /var/lib/mysql
```

Now we can start the server:
```sh
systemctl start mysql
```

### Advanced Usage

Do it all in one command -- this is a more efficient use of disk since it decompresses the download without saving it to disk first.
```sh
aws s3 cp s3://my-db-backup/xtrabackups/mysqlbackup.qp.xbs - | \
xbstream -x --parallel=8 && \
xtrabackup --decompress --remove-original --parallel=8 --target-dir=. && \
xtrabackup --prepare --target-dir=. && \
systemctl stop mysql && \
rm -rf /var/lib/mysql/* && \
xtrabackup --move-back --target-dir=. && \
chown -R mysql:mysql /var/lib/mysql && \
systemctl start mysql

```

1. Download single compressed file from S3 and send to stdin.
2. Pipe to decompress the single file (streaming to stdin) into multiple files/directories at the current location. The resulting files and directories still contain compressed files.
3. Decompress the resulting individual files and remove the original compressed files.
4. Prepare the backup (completes open transactions and rollbacks)
5. Stop the server
6. Remove existing files from the datadir
7. Restore the files
8. Change ownership of all files to `mysql:mysql`
9. Start the server

## References

* [Amazon S3](https://aws.amazon.com/s3/)
* [Percona XtraBackup v2.4](https://www.percona.com/doc/percona-xtrabackup/2.4/index.html)
* [Compressed Backup Recipe](https://www.percona.com/doc/percona-xtrabackup/2.4/backup_scenarios/compressed_backup.html)
* [Innobackupex Option Reference](https://www.percona.com/doc/percona-xtrabackup/2.4/innobackupex/innobackupex_option_reference.html)
* [Innobackupex Compressed Backup Recipe](https://www.percona.com/doc/percona-xtrabackup/2.4/howtos/recipes_ibkx_compressed.html)
