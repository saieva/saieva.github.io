---
layout: post
title:  "chef-run Compatibility with Amazon Linux 2022"
date:   2022-09-14 00:00:01 -0400
categories: AWS Linux
---
I've found two compatibility issues between Chef Workstation (ie, the `chef-run` command) and Amazon Linux 2022 (AL2022).
The versions tested were:

- Chef Workstation v22.7.1006 for macOS 12 on aarch64 ([download here][ChefWorkstationDownloadURL])
- Amazon Linux 2022 (AL2022) AMI 2022.0.20220824.0 arm64 HVM kernel-5.15 (ami-09d6dff71c75d903e)

For the first issue, AL2022 uses a version of OpenSSH that deprecated `ssh-rsa` key types. Therefore, the `chef-run` command
won't connect (ie, won't `ssh`) to a server with an RSA `--identity-file`. There are two options to resolve this issue:

1. Use an `ed25519` key pair to start the AL2022 EC2 instance and for the `chef-run --identify-file` parameter.
(This is the preferred option to avoid the interactive change in the next recommendation.) Instructions for creating a new
EC2 key pair are [here][EC2CreateKeyPairURL].
1. Otherwise, if it's necessary to use an RSA key pair, on the AL2022 server, edit
`/usr/share/crypto-policies/DEFAULT/opensshserver.txt` and add `ssh-rsa` to the `PubkeyAcceptedKeyTypes` list.

The second compatibility issue occurs when a Chef Cookbook fails to converge with the error:

    [2022-09-01T14:27:44-04:00] ERROR: stderr: /opt/chef/embedded/bin/ruby:
    error while loading shared libraries: libcrypt.so.1:
    cannot open shared object file: No such file or directory

- Resolve this issue by installing `libxcrypt-compat` from Amazon's default package repository (ie, `yum -y install libxcrypt-compat`).

A new package install request for `libxcrypt-compat` was submitted through the Amazon Linux 2022 repository on GitHub.
For details and status see [https://github.com/amazonlinux/amazon-linux-2022/issues/182][AL2022PackageInstallRequestURL].

[ChefWorkstationDownloadURL]: https://www.chef.io/downloads/get-started/desktop-management
[EC2CreateKeyPairURL]: https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/create-key-pairs.html
[AL2022PackageInstallRequestURL]: https://github.com/amazonlinux/amazon-linux-2022/issues/182
