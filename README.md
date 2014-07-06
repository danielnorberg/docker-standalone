docker-standalone
=================

A script for running a standalone docker daemon with a single container

Prerequisites
-------------
* Docker 1.0.0+ running and using:
  * The default native execution driver
  * The AUFS storage driver

Usage Examples
--------------
```
$ sudo ./docker-standalone busybox sh -c "while :; do date; sleep 1; done"
Pulling repository busybox
37fca75d01ff: Download complete
a9eb17255234: Download complete
fd5373b3d938: Download complete
d200959a3e91: Download complete
511136ea3c5a: Download complete
42eed7f1bf2a: Download complete
f06b02872d52: Download complete
c120b7cab0b0: Download complete
1f5049b3536e: Download complete
120e218dd395: Download complete
Starting standalone daemon: /var/lib/docker-standalone/479b4956670b8d242a63c86b6f79745c
Started container: busybox sh -c while :; do date; sleep 1; done
^CKilling container
Removing daemon
```

Modus Operandi
--------------

docker-standalone goes through the following step when running a container:

* Pull the image using the resident docker daemon
* Create a random directory in `/var/lib/docker-standalone/`
* Symlink the container images from the resident docker daemon
* Run the container and wait for it to exit
* Clean up after container exits or script is terminated


Why?
----
Docker being a daemon is not always great. Inside docker, there's a minimal
non-daemon git-like tool trying to escape. This is a bit of an experiment that
realizes this using the docker daemon as-is.


Caveats
-------
* There be dragons here.

* Currently uses `docker0` bridge so container ip address allocation will collide.


TODO
----
* Allocate unique addresses for each standalone container and use `docker run --bip`.

* Allow passing in `-p`, `-e`, etc.

* Add a `--cache` argument for specifying a reusable directory for caching
  pulled container images in order to avoid depending on the resident docker daemon.

* Implement the non-daemon mode of operation in docker itself. This might as
  simple as just combining the daemon and cli modes. I.e., essentially allowing
  something like `docker -d run ...`.

