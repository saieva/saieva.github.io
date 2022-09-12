---
layout: post
title:  "Running Squeezebox Server on AWS Graviton Systems"
date:   2022-09-12 00:00:01 -0400
categories: AWS Linux Audiophile
---
For anyone interested in running Logitech Media Server (LMS aka Squeezebox Server) on AWS EC2 instances with
Graviton processors, here is a tested configuration with a list of additional packages that are required.

**Logitech Media Server**

- v8.3.0 ARM Linux Tarball (ARM EABI) downloaded from the [slimdevices.com nightly builds][LMS83NightlyURL].

**Instance Type and Operating System**

- Amazon T4g.small (2 vCPUs and 2 GB of memory). T4g instances run Amazon's 2nd generation ARM-based
Graviton processors. Until December 31, 2022, Amazon is offering T4g.small instances on a *free trial*
for up to 750 hours/month.
- Amazon Linux 2022 (AL2022) AMI 2022.0.20220824.0 arm64 HVM kernel-5.15 (ami-09d6dff71c75d903e).
AL2022 is currently in release candidate status. For General Availability (GA) dates, see the
[Amazon Linux 2022 FAQ][AL2022FAQURL] page.

**OS Package Dependencies**

- `perl-deprecate`
- `perl-I18N-LangTags`
- `perl-lib perl-version`
- `perl-FindBin`
- `perl-Sys-Hostname`
- `perl-Time-HiRes`
- `perl-CGI`
- `perl-Digest-MD5`
- `perl-IO-Socket-SSL`

All dependencies were installed from Amazon's standard package repository (using the `yum` command).

One additional note about package dependencies, without `perl-deprecate` installed the LMS startup command
(ie, `slimserver.pl`) will produce the following error message:

```
The following CPAN modules were found but cannot work with Logitech Media Server:
  CGI::Cookie (loaded <not found>, need 1.27)
```

The message doesn't make it clear that `perl-deprecate` is misssing and the issue was reported on the Logitech/slimserver
repository on GitHub. For details and status see [https://github.com/Logitech/slimserver/issues/810][LMSIssueURL].

[LMS83NightlyURL]: https://downloads.slimdevices.com/nightly/?ver=8.3
[LMSIssueURL]: https://github.com/Logitech/slimserver/issues/810
[AL2022FAQURL]: https://aws.amazon.com/linux/amazon-linux-2022/faqs/
