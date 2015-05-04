---
layout: post
title: SysVinit, upstart & systemd
tags: linux init rhce refresher
---
[back]({{ site.baseurl }})
# {{ page.title }}

Brief refresher to init daemons employed in RHEL (and its derivatives) 5, 6, and 7.

## SysVinit

- Employed in RHEL 5
- [project page](http://savannah.nongnu.org/projects/sysvinit)
- [source](http://svn.savannah.nongnu.org/viewvc/sysvinit/trunk/?root=sysvinit),
  [github mirror](https://github.com/holcapek/sysvinit)

### General workings

- Services are configured to run at specified runlevels
- Services are started synchronously
- Dependencies between services are not solved out of box,
  must be stated in an explicit order of services being started

### Packages

- `SysVinit`
- `initscripts`
- `chkconfig`

### Configuration

- File
  - `/etc/inittab`
- Structure

    ```
    id:runlevels:action:process
    ```
- Example

    ```
    # default runlevel
    id:3:initdefault
    
    # executed once at boot time
    si::sysinit:/etc/rc.d/rc.sysinit
    
    # start services based on runlevel
    l0:0:wait:/etc/rc.d/rc 0
    l1:1:wait:/etc/rc.d/rc 1
    l2:2:wait:/etc/rc.d/rc 2
    l3:3:wait:/etc/rc.d/rc 3
    l4:4:wait:/etc/rc.d/rc 4
    l5:5:wait:/etc/rc.d/rc 5
    l6:6:wait:/etc/rc.d/rc 6
  
    # start 3 virtual terminals
    1:2345:respawn:/sbin/mingetty tty1
    2:2345:respawn:/sbin/mingetty tty2
    3:2345:respawn:/sbin/mingetty tty3
    ```

### Directories

- `/etc/rc.d/init.d/` contains services's init scripts

    ```
    /etc/rc.d/init.d/crond
    ```
- `/etc/rc.d/rc[0123456].d/` contains symlinks to services' init scripts while
  following this naming convention: K as for kill, S as for start, number defines order
  in which the services are stopped (killed) and started

    ```
    /etc/rc.d/rc0.d/K60crond -> ../init.d/crond
    /etc/rc.d/rc1.d/K60crond -> ../init.d/crond
    /etc/rc.d/rc2.d/S90crond -> ../init.d/crond
    /etc/rc.d/rc3.d/S90crond -> ../init.d/crond
    /etc/rc.d/rc4.d/S90crond -> ../init.d/crond
    /etc/rc.d/rc5.d/S90crond -> ../init.d/crond
    /etc/rc.d/rc6.d/K60crond -> ../init.d/crond
    ```

### Scripts

- `/etc/rc.d/rc.sysinit` is executed before anything else is executed 
- `/etc/rc.d/rc` TODO

### Tools

- `/sbin/telinit` allows for changing runlevel

    ```
    # enter (change to) runlevel 5
    /sbin/telinit 5
    ```
- `/sbin/service` allows for starting and stopping a service on demand

    ```
    # stop crond service no matter whether it is supposed to be running
    # in the current runlevel
    /sbin/service crond stop
    ```
- `/sbin/chkconfig` helps with creating and removing the symlinks within `/etc/rc.d/rc[0123456]`

    ```
    # start crond at runlevels defined by chkconfig or LSB header
    /sbin/chkconfig --add crond

    # stop crond at runlevel 2
    /sbin/chkconfig --level 2 crond off
    ```
     
### Further readings

- [chkconfig header](https://fedoraproject.org/wiki/Packaging:SysVInitScript#Chkconfig_Header)
- [LSB header](https://fedoraproject.org/wiki/Packaging:SysVInitScript#LSB_Header)
- [Comment Conventions for Init Scripts](http://refspecs.linuxfoundation.org/LSB_3.1.0/LSB-Core-generic/LSB-Core-generic/initscrcomconv.html)

## upstart

- Employed in RHEL 6.
- [project page](http://upstart.ubuntu.com/)
- [source](http://bazaar.launchpad.net/~upstart-devel/upstart/trunk/files)

### General workings

- Jobs are started asynchronously
- Dependencies between jobs are stated by the events at which
  the jobs are started and stopped

### Packages

- `upstart`

### Configuration

- Files
  - `/etc/init/*.conf`
  - `/etc/init/*.override`

- Structure

    ```
    # when to start and stop
    start on EVENT
    stop on EVENT
    
	# JOB means either exec or script stanza
    
	# executed after starting event emitted
    pre-start JOB
    
    # executed after started event emitted
    post-start JOB
    
    # the main job definition (exec and script are mutually exclusive)
	JOB
    
    # executed before stopping event emitted
	pre-stop JOB
    
    # executed after stopped event emitted
    
    # jobs is a task (short-lived process) rather
    # than a service (long-running process) task
    task

    # the job has several instances
    instance INSTANCE

    ``` 

### Tools

- `/sbin/{start,stop,restart,reload,status}` allows for starting, stopping, restarting,
  reloading, or getting the status of the job, respectively (actually symlinks to `/sbin/initctl`)

    ```
    /sbin/status control-alt-delete
    ```
- `/sbin/initctl` allows the above and more, eg. emitting a cutstom event, listing known jobs

    ```
    # list jobs
    /sbin/initctl list

    # emit an event
    /sbin/initctl emit EVENT
    ```
    
### SysVinit compatibilty

- Default runlevel is read from `/etc/inttab`, `initdefault` is the only entry there.
- Job `/etc/init/rc.conf` executes SysVinit service init scripts in `/etc/rc.d/rc[0123456].d/`

### Further readings

- [Upstart Cookbook](http://upstart.ubuntu.com/cookbook/)

## systemd

- Employed in RHEL 7
- [project page](http://www.freedesktop.org/wiki/Software/systemd/)
- [source](http://cgit.freedesktop.org/systemd/systemd/)
  ([github mirror](https://github.com/systemd/systemd))

### Packages

- `systemd`
- `systemd-sysv`
