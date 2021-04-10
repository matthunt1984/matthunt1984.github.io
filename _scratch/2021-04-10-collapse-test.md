---
markdown: CommonMarkGhPages
title:  "bloop"
date:   2021-03-27 17:02:39 +0000
last_modified_at: 2021-03-28
categories: tech
tags: diy-tech,aws,terraform
#toc: true
#toc_sticky: true
---
---

Collapse test
https://github.community/t/collapsible-markdown-inside-details-summary-summary-details-fails-to-render/10489


# A collapsible section with markdown
<details>
  <summary>Click to expand!</summary>
  
  ## Heading
  1. A numbered
  2. list
     * With some
     * Sub bullets
</details>


```
root@DESKTOP-TO058IA:~/repos/my-archive (main *%=)# terraform show
# aws_s3_bucket.media-archive-bucket:
resource "aws_s3_bucket" "media-archive-bucket" {
    acl                         = "private"
    arn                         = "arn:aws:s3:::my-media-archive-20210306211714538000000001"
    bucket                      = "my-media-archive-20210306211714538000000001"
    bucket_domain_name          = "my-media-archive-20210306211714538000000001.s3.amazonaws.com"
    bucket_prefix               = "my-media-archive-"
    bucket_regional_domain_name = "my-media-archive-20210306211714538000000001.s3.eu-west-1.amazonaws.com"
    force_destroy               = false
    hosted_zone_id              = "Z1BKCTXD74EZPE"
    id                          = "my-media-archive-20210306211714538000000001"
    region                      = "eu-west-1"
    request_payer               = "BucketOwner"
    tags                        = {}

    lifecycle_rule {
        abort_incomplete_multipart_upload_days = 0
        enabled                                = true
        id                                     = "media"
        tags                                   = {}

        transition {
            days          = 10
            storage_class = "DEEP_ARCHIVE"
        }
    }

    versioning {
        enabled    = false
        mfa_delete = false
    }
}
```
