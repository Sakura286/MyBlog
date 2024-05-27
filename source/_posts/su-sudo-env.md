---
title: su 与 sudo 的环境变量问题
date: 2024-04-29 00:03:30
tags:
---
#!/bin/bash

set -x

REPO="yahboom-car-revyos"

aptly publish drop bullseye .
aptly repo add $REPO $(ls | grep -E "\.deb$" | tr "\n" " ") $(ls | grep -E "revyos[0-9]\.dsc$" | tr "\n" " ")

SNAPSHOT=$(date "+%m%d_%M%S")
aptly snapshot create $SNAPSHOT from repo $REPO
aptly publish snapshot $SNAPSHOT
