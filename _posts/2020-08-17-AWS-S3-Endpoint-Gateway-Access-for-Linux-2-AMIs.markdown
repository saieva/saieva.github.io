---
layout: post
title:  "AWS S3 Endpoint Gateway Access for Linux 2 AMIs (Resolving HTTP 403 Forbidden Error)"
date:   2020-08-17 00:00:01 -0400
categories: AWS Linux
---
An AWS Linux 2 EC2 instance running in a VPC configured with an S3 Endpoint Gateway to access update
repositories received the following error when running the `yum update` command:

    # yum update
    Loaded plugins: extras_suggestions, langpacks, priorities, update-motd
    Could not retrieve mirrorlist http://amazonlinux.us-east-1.amazonaws.com/2/core/latest/x86_64/mirror.list error was
    14: HTTP Error 403 - Forbidden

The S3 Endpoint Gateway was configured in the VPC using the following documentation:

- [https://docs.aws.amazon.com/vpc/latest/userguide/vpc-endpoints.html](https://docs.aws.amazon.com/vpc/latest/userguide/vpc-endpoints.html)
- [https://docs.aws.amazon.com/vpc/latest/userguide/vpce-gateway.html](https://docs.aws.amazon.com/vpc/latest/userguide/vpce-gateway.html)
- [https://docs.aws.amazon.com/vpc/latest/userguide/vpc-endpoints-s3.html](https://docs.aws.amazon.com/vpc/latest/userguide/vpc-endpoints-s3.html)

The error persisted even though the configuration was triple-checked, including Network ACLs, Security Groups, and the Endpoint Policy (which was copied from the setup documentation).

The issue was resolved by extending the VPC Endpoint Policy to allow access to Linux 2 repositories;
the specific change was to add `"arn:aws:s3:::amazonlinux.*.amazonaws.com/*"` to the Resource list.

    { "Version": "2008-10-17",
      "Statement": [ {
        "Sid": "Amazon Linux AMI Repository Access",
        "Effect": "Allow",
        "Principal": "*",
        "Action": "s3:GetObject",
        "Resource": [
          "arn:aws:s3:::packages.*.amazonaws.com/*",
          "arn:aws:s3:::repo.*.amazonaws.com/*",
          "arn:aws:s3:::amazonlinux.*.amazonaws.com/*" ] } ]
    }

