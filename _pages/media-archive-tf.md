---
title: "Terraform extract"
#permalink: /posts/
layout: posts

#Accessible via : http://127.0.0.1:4000/_pages/media-archive-tf/
---

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
  region  = "eu-west-1" #or nearer
}

resource "aws_s3_bucket" "media-archive-bucket" {
  bucket_prefix = "my-media-archive-"
  acl           = "private" #?authenticated-read? or grant?

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
