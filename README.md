# osixia/backup-manager

[![](https://badge.imagelayers.io/osixia/backup-manager:latest.svg)](https://imagelayers.io/?images=osixia/backup-manager:latest 'Get your own badge on imagelayers.io') | Latest release: 0.1.4 -  [Changelog](CHANGELOG.md) | [Docker Hub](https://hub.docker.com/r/osixia/backup-manager/) 

An image to run periodicaly backup-manager.

## Quick start

    # Run Backup Manager image
    docker run --volume /host/data:/data-to-backup --detach osixia/backup-manager:0.1.4

#### Backup directory and data persitance

Backups are created by default in the directory `/data/backup` that has been declared as a volume, so your backup files are saved outside the container in a data volume.

For more information about docker data volume, please refer to :

> [https://docs.docker.com/userguide/dockervolumes/](https://docs.docker.com/userguide/dockervolumes/)

## Environment Variables

Environment variables defaults are set in **image/environment/default.yaml**

See how to [set your own environment variables](#set-your-own-environment-variables)


- **BACKUP_MANAGER_TARBALL_DIRECTORIES**: Directories to backup: paths without spaces in their name. Defaults to `/data-to-backup /data-to-backup2`.

- **BACKUP_MANAGER_CRON_EXP**: Cron expression to schedule backup-manager execution. Defaults to `"0 4 * * *"`. Every days at 4am.

- **BACKUP_MANAGER_TTL**: Backup TTL in days. Defaults to `15`.

Upload configuration:

- **BACKUP_MANAGER_UPLOAD_METHOD**: Upload method. Defaults to `ftp`.

- **BACKUP_MANAGER_UPLOAD_HOSTS**: Upload to this ftp hosts. Defaults to `ftp.example.org`.

- **BACKUP_MANAGER_UPLOAD_FTP_USER**: Ftp user. Defaults to `ftp-user`.
- **BACKUP_MANAGER_UPLOAD_FTP_PASSWORD**: Ftp password. Defaults to `ftp-password`.
- **BACKUP_MANAGER_UPLOAD_DESTINATION**: Upload to this ftp directory.  Defaults to `/`.
- **BACKUP_MANAGER_UPLOAD_TTL**: Backup TTL on the ftp hosts in days. Defaults to `60`.

Encryption configuration:

- **BACKUP_MANAGER_ENCRYPTION**: Encrypt backups. Defaults to `false`.
- **BACKUP_MANAGER_ENCRYPTION_RECIPIENT**: GPG recipient. Defaults to `Mike Ross`.

More help: https://raw.githubusercontent.com/sukria/Backup-Manager/master/doc/user-guide.txt

### Set your own environment variables

#### Use command line argument
Environment variables can be set by adding the --env argument in the command line, for example:

	docker run --env BACKUP_MANAGER_TARBALL_DIRECTORIES="/home/billy" \
	--detach osixia/backup-manager:0.1.4

#### Link environment file

For example if your environment file is in :  /data/backup-manager/environment/my-env.yaml

	docker run --volume /data/backup-manager/environment/my-env.yaml:/container/environment/01-custom/env.yaml \
	--detach osixia/backup-manager:0.1.4

Take care to link your environment file to `/container/environment/XX-somedir` (with XX < 99 so they will be processed before default environment files) and not  directly to `/container/environment` because this directory contains predefined baseimage environment files to fix container environment (INITRD, LANG, LANGUAGE and LC_CTYPE).

#### Make your own image or extend this image

This is the best solution if you have a private registry. Please refer to the [Advanced User Guide](#advanced-user-guide) just below.

## Advanced User Guide

### Extend osixia/backup-manager:0.1.4 image

If you need to add your custom TLS certificate, bootstrap config or environment files the easiest way is to extends this image.

Dockerfile example:

    FROM osixia/backup-manager:0.1.4
    MAINTAINER Your Name <your@name.com>

    ADD environment /container/environment/01-custom
    ADD gpg-keys /container/service/gpg/assets


### Make your own backup-manager image

Clone this project :

	git clone https://github.com/osixia/docker-backup-manager
	cd docker-backup-manager

Adapt Makefile, set your image NAME and VERSION, for example :

	NAME = osixia/backup-manager
	VERSION = 0.1.4

	becomes :
	NAME = billy-the-king/backup-manager
	VERSION = 0.1.0

Build your image :

	make build

Run your image :

	docker run -d billy-the-king/backup-manager:0.1.0

### Tests

We use **Bats** (Bash Automated Testing System) to test this image:

> [https://github.com/sstephenson/bats](https://github.com/sstephenson/bats)

Install Bats, and in this project directory run :

	make test

### Kubernetes

Kubernetes is an open source system for managing containerized applications across multiple hosts, providing basic mechanisms for deployment, maintenance, and scaling of applications.

More information:
- http://kubernetes.io
- https://github.com/kubernetes/kubernetes

A kubernetes example is available in **example/kubernetes**

### Under the hood: osixia/light-baseimage

This image is based on osixia/light-baseimage.
More info: https://github.com/osixia/docker-light-baseimage

## Changelog

Please refer to: [CHANGELOG.md](CHANGELOG.md)
