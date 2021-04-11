---
title:  "Using S3 for long-term photo backups"
date:   2021-03-27 17:02:39 +0000
last_modified_at: 2021-03-28
categories: tech
tags: diy-tech,aws,terraform
#toc: true
#toc_sticky: true
---

## Intro

#https://www.hashicorp.com/blog/detecting-and-managing-drift-with-terraform

`terraform refresh`
`terraform import`
`terraform show`

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

'''
 "resources": [
    {
      "mode": "managed",
      "type": "aws_s3_bucket",
      "name": "media-archive-bucket",
      "provider": "provider[\"registry.terraform.io/hashicorp/aws\"]",
      "instances": [
        {
          "schema_version": 0,
          "attributes": {
            "acceleration_status": "",
            "acl": "private",
            "arn": "arn:aws:s3:::my-media-archive-20210306211714538000000001",
            "bucket": "my-media-archive-20210306211714538000000001",
            "bucket_domain_name": "my-media-archive-20210306211714538000000001.s3.amazonaws.com",
            "bucket_prefix": "my-media-archive-",
            "bucket_regional_domain_name": "my-media-archive-20210306211714538000000001.s3.eu-west-1.amazonaws.com",
            "cors_rule": [],
            "force_destroy": false,
            "grant": [],
            "hosted_zone_id": "Z1BKCTXD74EZPE",
            "id": "my-media-archive-20210306211714538000000001",
            "lifecycle_rule": [
              {
                "abort_incomplete_multipart_upload_days": 0,
                "enabled": true,
                "expiration": [],
                "id": "media",
                "noncurrent_version_expiration": [],
                "noncurrent_version_transition": [],
                "prefix": "",
                "tags": {},
                "transition": [
                  {
                    "date": "",
                    "days": 10,
                    "storage_class": "DEEP_ARCHIVE"
                  }
                ]
              }
            ],
            "logging": [],
            "object_lock_configuration": [],
            "policy": null,
            "region": "eu-west-1",
            "replication_configuration": [],
            "request_payer": "BucketOwner",
            "server_side_encryption_configuration": [],
            "tags": {},
            "versioning": [
              {
                "enabled": false,
                "mfa_delete": false
              }
            ],
            "website": [],
            "website_domain": null,
            "website_endpoint": null
          },
          "sensitive_attributes": [],
          "private": "bnVsbA=="
        }
      ]
    }
  ]
'''

'''
root@DESKTOP-TO058IA:~/repos/my-archive (main *%=)# terraform plan
aws_s3_bucket.media-archive-bucket: Refreshing state... [id=my-media-archive-20210306211714538000000001]

An execution plan has been generated and is shown below.
Resource actions are indicated with the following symbols:
  ~ update in-place

Terraform will perform the following actions:

  # aws_s3_bucket.media-archive-bucket will be updated in-place
  ~ resource "aws_s3_bucket" "media-archive-bucket" {
        id                          = "my-media-archive-20210306211714538000000001"
        tags                        = {}
        # (10 unchanged attributes hidden)

      ~ lifecycle_rule {
            id                                     = "media"
          + prefix                                 = "media/"
          ~ tags                                   = {
              + "autoclean" = "true"
              + "rule"      = "media"
            }
            # (2 unchanged attributes hidden)

          - transition {
              - days          = 10 -> null
              - storage_class = "DEEP_ARCHIVE" -> null
            }
          + transition {
              + days          = 365
              + storage_class = "DEEP_ARCHIVE"
            }
          + transition {
              + days          = 7
              + storage_class = "GLACIER"
            }
        }

        # (1 unchanged block hidden)
    }

Plan: 0 to add, 1 to change, 0 to destroy.

------------------------------------------------------------------------

Note: You didn't specify an "-out" parameter to save this plan, so Terraform
can't guarantee that exactly these actions will be performed if
"terraform apply" is subsequently run.
'''

'''
resource "aws_iam_user" "console-archiver" {

}
'''


'''
root@DESKTOP-TO058IA:~/repos/my-archive (main *=)# terraform state show aws_iam_user.console-archiver
# aws_iam_user.console-archiver:
resource "aws_iam_user" "console-archiver" {
    arn       = "arn:aws:iam::907364036946:user/console-archiver"
    id        = "console-archiver"
    name      = "console-archiver"
    path      = "/"
    tags      = {}
    unique_id = "AIDA5GQY4OFJMIRGEN5D7"
}

root@DESKTOP-TO058IA:~/repos/my-archive (main *=)# terraform plan

Error: Missing required argument

  on archive.tf line 34, in resource "aws_iam_user" "console-archiver":
  34: resource "aws_iam_user" "console-archiver" {

The argument "name" is required, but no definition was found.
'''

'''
root@DESKTOP-TO058IA:~/repos/my-archive (main *=)# terraform import aws_iam_user_policy_attachment.archiver-s3-full-access console-archiver/arn:aws:iam::aws:policy/AmazonS3FullAccess
aws_iam_user_policy_attachment.archiver-s3-full-access: Importing from ID "console-archiver/arn:aws:iam::aws:policy/AmazonS3FullAccess"...
aws_iam_user_policy_attachment.archiver-s3-full-access: Import prepared!
  Prepared aws_iam_user_policy_attachment for import
aws_iam_user_policy_attachment.archiver-s3-full-access: Refreshing state... [id=console-archiver-arn:aws:iam::aws:policy/AmazonS3FullAccess]

Import successful!

The resources that were imported are shown above. These resources are now in
your Terraform state and will henceforth be managed by Terraform.
'''

'''
root@DESKTOP-TO058IA:~/repos/my-archive (main *=)# terraform import aws_iam_access_key.console-archiver-key AKIA5GQY4OFJBESV65WW
aws_iam_access_key.console-archiver-key: Importing from ID "AKIA5GQY4OFJBESV65WW"...
aws_iam_access_key.console-archiver-key: Import prepared!
  Prepared aws_iam_access_key for import
aws_iam_access_key.console-archiver-key: Refreshing state... [id=AKIA5GQY4OFJBESV65WW]

Import successful!

The resources that were imported are shown above. These resources are now in
your Terraform state and will henceforth be managed by Terraform.

root@DESKTOP-TO058IA:~/repos/my-archive (main *=)# terraform plan

Error: Missing required argument

  on archive.tf line 43, in resource "aws_iam_access_key" "console-archiver-key":
  43: resource "aws_iam_access_key" "console-archiver-key" {

The argument "user" is required, but no definition was found.

root@DESKTOP-TO058IA:~/repos/my-archive (main *=)# terraform plan
aws_iam_user.console-archiver: Refreshing state... [id=console-archiver]
aws_s3_bucket.media-archive-bucket: Refreshing state... [id=my-media-archive-20210306211714538000000001]
aws_iam_user_policy_attachment.archiver-s3-full-access: Refreshing state... [id=console-archiver-arn:aws:iam::aws:policy/AmazonS3FullAccess]
aws_iam_access_key.console-archiver-key: Refreshing state... [id=AKIA5GQY4OFJBESV65WW]

No changes. Infrastructure is up-to-date.

This means that Terraform did not detect any differences between your
configuration and real physical resources that exist. As a result, no
actions need to be performed.
root@DESKTOP-TO058IA:~/repos/my-archive (main *=)# terraform apply
aws_iam_user.console-archiver: Refreshing state... [id=console-archiver]
aws_s3_bucket.media-archive-bucket: Refreshing state... [id=my-media-archive-20210306211714538000000001]
aws_iam_user_policy_attachment.archiver-s3-full-access: Refreshing state... [id=console-archiver-arn:aws:iam::aws:policy/AmazonS3FullAccess]
aws_iam_access_key.console-archiver-key: Refreshing state... [id=AKIA5GQY4OFJBESV65WW]

Apply complete! Resources: 0 added, 0 changed, 0 destroyed.
'''