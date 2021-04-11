---
title: "Terraform for my media archive"
permalink: /ref/:basename

#Accessible via : http://127.0.0.1:4000/ref/media-archive-tf
---

This was originaly used in my [previous post]({% post_url 2021-03-27-using-s3-for-long-term-photo-backups %})

```terraform
terraform {
  required_providers {
    aws = {
      source  = "hashicorp/aws"
      version = "~> 3.27"
    }
  }
}

provider "aws" {
  profile = "default"
  region  = "eu-west-1"
}

resource "aws_s3_bucket" "media-archive-bucket" {
  bucket_prefix = "my-media-archive-"
  acl           = "private" 
  
  lifecycle_rule {
    id      = "media"
    enabled = true

    transition {
      days          = 10
      storage_class = "DEEP_ARCHIVE"
    }
  }

  lifecycle {
    prevent_destroy = true
  }
}

resource "aws_iam_user" "api-archvier" {
  name = "api-archvier"
}

resource "aws_iam_user_policy_attachment" "archiver-s3-full-access" {
  user       = aws_iam_user.api-archvier.name
  policy_arn = "arn:aws:iam::aws:policy/AmazonS3FullAccess"
}

resource "aws_iam_access_key" "api-archvier-key" {
  user   = aws_iam_user.api-archvier.name
  status = "Inactive"
}
```
