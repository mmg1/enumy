<a href="https://scan.coverity.com/projects/luke-goddard-enumy"><img alt="Coverity Scan Build Status" src="https://scan.coverity.com/projects/20962/badge.svg"/></a>
[![Maintenance](https://img.shields.io/badge/Maintained%3F-yes-green.svg)](https://github.com/luke-goddard/enumy/graphs/commit-activity)
[![GitHub license](https://img.shields.io/github/license/Naereen/StrapDown.js.svg)](https://github.com/Naereen/StrapDown.js/blob/master/LICENSE)
[![Total alerts](https://img.shields.io/lgtm/alerts/g/luke-goddard/enumy.svg?logo=lgtm&logoWidth=18)](https://lgtm.com/projects/g/luke-goddard/enumy/alerts/)
[![Help Wanted](https://img.shields.io/github/issues/luke-goddard/enumy/help%20wanted?color=green)](https://github.com/luke-goddard/enumy/issues?q=is%3Aissue+is%3Aopen+label%3A%22help+wanted%22)

# Enumy

<img src="https://i.imgur.com/luC3mTu.png"  align="right" width="300" height="200"/>

Enumy is an __ultra fast portable executable__ that you drop on target Linux machine during a pentest or CTF in the post exploitation phase. Running enumy will enumerate the box for __common security vulnerabilities__.

Enumy is portable executable that you drop on target Linux machine during a pentest or CTF in the post exploitation phase. Running enumy will enumerate the box for common security vulnerabilities.

## Installation

You can download the final binary from the release x86 or x64 tab. _Statically linked to musl_
Transfer the final enumy binary to the target machine.

- [latest release](https://github.com/luke-goddard/enumy/releases)

```shell
./enumy
```

## Who Should Use Enumy

- Pentester can run on a target machine raisable issues for their reports.
- CTF players can use it identify things that they might have missed.
- People who are curious to know how many isues enumy finds on their local machine?

## Options

```shell
$ ./enumy64 -h

 ▄█▀─▄▄▄▄▄▄▄─▀█▄  _____
 ▀█████████████▀ |   __|___ _ _ _____ _ _
     █▄███▄█     |   __|   | | |     | | |
      █████      |_____|_|_|___|_|_|_|_  |
      █▀█▀█                          |___|


------------------------------------------

Enumy - Used to enumerate the target environment and look for common
security vulnerabilities and hostspots

 -o <loc>     Save results to location
 -i <loc>     Ignore files in this directory (usefull for network shares)
 -w <loc>     Only walk files in this directory (usefull for devlopment)
 -t <num>     Threads (default 4)
 -f           Run full scans
 -s           Show missing shared libaries
 -d           Debug mode
 -n           Enabled ncurses
 -h           Show help
 ```

## Compilation

To compile during _devlopment_, make and libcap libary is all that is required.

```shell
sudo apt-get install libcap-dev
make
```

To remove the glibc dependency and statically link all libaries/compile with musl do the following. _Note to do this you will have to have docker installed to create the apline build environment._

```shells
./build.sh 64bit
./build.sh 32bit
./build.sh all
cd output
```

## Scans That've Been Implemented

Below is the ever growing list of scans that have been implemented.

| Scan Type  | Quick scan | Full Scan | Implemented |
| ------------- | ------------- | -------------- | ----------- |
| [SUID/GUID Scan](#suid-guid-scan) | [x] Enabled | [x] Enabled | [x] True |
| [File Capabilities Scan](#file-capabilities-scan) | [x] Enabled | [x] Enabled | [x] True |
| [Intresting Files Scan](#intresting-files-scan) | [x] Enabled | [x] Enabled | [x] True |
| [Coredump Scan](#coredump-scan) | [x] Enabled| [x] Enabled | [x] True |
| [Breakout Binaries Scan](#breakout-binary-scan)| [x] Enabled | [x] Enabled | [x] True |
| [SSHD Configuration Scan](#ssh-misconfiguration-scan) | [x] Enabled | [x] Enabled | [x] True |
| [Sysctl Scan](#sysctl-parameter-hardening) | [x] Enabled | [x] Enabled | [x] True |
| [Living Off The Land Scan](#living-off-the-land-scan) | [x] Enabled | [x] Enabled | [x] True |
| [Dynamic Shared Object Injection Scan](#dynamic-shared-object-injection-scan) | [ ] Disabled | [x] Enabled | [x] True |
| Current User Scan | [ ] Disabled | [ ] Disabled | [ ] False |
| Permissions Scan | [ ] Disabled | [ ] Disabled | [ ] False |
| Docker Scan | [ ] Disabled | [ ] Disabled | [ ] False |
| Environment Scan | [ ] Disabled | [ ] Disabled | [ ] False |
| Privilaged Access Scan | [ ] Disabled | [ ] Disabled | [ ] False |
| Networking Scan | [ ] Disabled | [ ] Disabled | [ ] False |
| System Info Scan | [ ] Disabled | [ ] Disabled | [ ] False |
| Verion Information Scan | [ ] Disabled | [ ] Disabled | [ ] False |
| Default Weak Credentials Scan | [ ] Disabled | [ ] Disabled | [ ] False |
| Weak Crypto Scan | [ ] Disabled | [ ] Disabled | [ ] False |

## Scan Times

Changing the default number of threads is pretty pointless __unless__  you're running a full scan. A full scan will do a lot more IO so more threads greatly decrease scan times. These are the scan times with a i7-8700k and 2 million files scanned.

### Benchmarks

| Scan Type  | Files Scanned | Threads | Time       |
| -----------| ------------- | --------| ---------- |
| Quick scan |  1.8 Million  | 1       | 54 seconds |
| Quick scan |  1.8 Million  | 2       | 26 seconds |
| Quick scan |  1.8 Million  | 4       | 15 seconds |
| Quick scan |  1.8 Million  | 6       | 15 seconds |
| Quick scan |  1.8 Million  | 12      | 20 seconds |
| Full scan  |  1.8 Million  | 1       | 196 seconds |
| Full scan  |  1.8 Million  | 2       | 93 seconds |
| FUll scan  |  1.8 Million  | 4       | 47 seconds |
| Full scan  |  1.8 Million  | 6       | 30 seconds |
| Full scan  |  1.8 Million  | 12      | 29 seconds |

### Scan types

#### SUID GUID Scan

The idea of this scan is enumerate the system looking for [SUID](https://www.hackingarticles.in/linux-privilege-escalation-using-suid-binaries/)/GUID binaries that are abnormal, or have weak permissions that can be exploited.

#### File Capabilities Scan

Recently the Linux kernel supports [capablities](https://www.man7.org/linux/man-pages/man7/capabilities.7.html), this is the prefered way to give a file a subset of root's powers to mitigate risk. Although this is a much safer way of doing things, if you're lucky enough to find abnormal capabilities set on a file then it's quite possible that you can exploit the executable to gain higher access. Enumy will check the capabilties set on all executable files on the system.

#### Interesting Files Scan

This is more of a generic scan that will try and categorize a file based off it's contents, file extension and file name. Enumy will look for files such as private keys, passwords and backup files.

#### Coredump Scan

Coredump files are a type of ELF file that contains a process's address space when the program terminates unexpectedly. Now imagine if this process's memory was readable and contained sensative information. Or even more exciting, this coredump could be for an internally developed tool that seg faulted, allowing you to develop a zero day.

#### Breakout Binary Scan

Some file should never have SUID bit set, it quite common for a lazy sys admin to give a file like docker, ionice, hexdump SUID make a bash script work or there life easier. This scan tries to find some known bad SUID binaries.

#### Sysctl Parameter Hardening

[Sysctl](https://linux.die.net/man/8/sysctl) is used to modify kernel parameters at runtime. It's also possible to query these kernel parameters and check to see if important secutiry measures like ASLR are enabled.

#### Living Off The Land scan

Living off the land is a technique used where attackers weponize what's allready on the system. They do this to remain stealthy amongst other reasons. This scan would enumerate the files that an attacker would be looking for.

#### Dynamic Shared Object Injection Scan

This scan will parse ELF files for their dependencies. If we have write access to any of these dependencies or write access to any DT_RPATH and DT_RUNPATH values then we can create our own malicious shared object into that executable potentiall compromizing the system.

#### SSH Misconfiguration Scan

SSH is one of one of the most common services that you will find in the real world. It's also quite easy to misconfigure it. This scan will check to see if it can be hardened in anyway.

## How To Contribute

- If you can think of a scan idea that has not been implemented, raise it as an issue.
- If you know how to program, make a pull request :)
