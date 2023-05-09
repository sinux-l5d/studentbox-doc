---
title: "Setup"
draft: true
tags: []
aliases: []
---

# Setup

This can be automated with AWS.

In order to use this app, the administrator should first have a linux server with enough memory/storage capacity to run containers.

In my case, I'll use a EC2 t2.micro instance of Debian 11.

0. `sudo apt update && sudo apt upgrade && sudo apt install -y gpg`
1. Add alvistack PPA repository to have last version of podman
	1. `source /etc/os-release`
	2. `wget http://downloadcontent.opensuse.org/repositories/home:/alvistack/Debian_$VERSION_ID/Release.key -O alvistack_key`
	3. `cat alvistack_key | gpg --dearmor | sudo tee /etc/apt/trusted.gpg.d/alvistack.gpg >/dev/null`
	4. `echo "deb http://downloadcontent.opensuse.org/repositories/home:/alvistack/Debian_$VERSION_ID/ /" | sudo tee /etc/apt/sources.list.d/alvistack.list`
	5. `sudo apt update`
2. Check for required packages : `sudo apt install -y podman slirp4netns fuse-overlayfs uidmap`
3. Ensure podman 4 minimum is installed: `sudo podman version`
4. Setup for rootless: https://github.com/containers/podman/blob/main/docs/tutorials/rootless_tutorial.md
	1. To be rootless, the user running needs to have a range of UIDs in /etc/subuid and /etc/subgid. Check that both files contains `admin:100000:65536`, where admin is the user that will use studentbox
5. Try if podman is working with Docker's hello world: `podman run -it --rm docker.io/library/hello-world`. In my case, I had to start user's dbus because of a warning: `systemctl --user start dbus`.
6. Enable podman's socket for this userspace: `systemctl --user enable --now podman.socket`. This is required by Studentbox.
7. Finally, create a data directory and run the cli: `mkdir data && podman run --rm -it -v /run/user/1000/podman/:/podman/ -v $PWD/data:/data ghcr.io/sinux-l5d/studentbox/cli:latest`. If everything went good, you should have:
	```
	Trying to pull ghcr.io/sinux-l5d/studentbox/cli:latest...
	Getting image source signatures
	Copying blob 45ddd45372cb done
	Copying config d02c5687a7 done
	Writing manifest to image destination
	Storing signatures
	No containers
	```
