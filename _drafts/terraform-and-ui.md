---
title:  "Importing manually created infrastructure into Terraform"
#date:   2021-04-11 20:00:00 +0000, can't set date until finished
categories: tech
tags: aws,terraform
toc: true
toc_sticky: true
---

You might create resources manually via a GUI, or a command-line for various reasons, but particularly when you're learning a new resource, or optimising specific settings. GUIs typically have more advice and instructions etc that will make the process easier the first time.

Now terraform is great, and the basic terraform flow is relatively straightforward. 
![Terraform basic workflow]({{ "assets/images/terraform-exercise-api-archiver/basic-terraform-workflow.png" | relative_url}})

I had assumed there'd be a very similar process to do the opposote. Convert existing infrastructure into updated or new config files; but it seems there's nothing quite that straightforward! *At least not yet*.

**If you're using Oracle Cloud Infrastructure** - it seems that there is such an advanced tool in the form of [OCI Resource Descovery](https://registry.terraform.io/providers/hashicorp/oci/latest/docs/guides/resource_discovery):<BR/>*Terraform Resource Discovery can be used to discover deployed resources within a compartment and export them to Terraform configuration and state files.*
{: .notice--info}

If you're not using OCI, there are other options. If you're only considering minor drift from terraform created resources then this [blog post](#https://www.hashicorp.com/blog/detecting-and-managing-drift-with-terraform) has most of the details. However if you want to fully bring in resources you created manually we will need `terraform import`. The best resource I found on it was the [terraform import tutorial](https://learn.hashicorp.com/tutorials/terraform/state-import?in=terraform/state&utm_source=WEBSITE&utm_medium=WEB_IO&utm_offer=ARTICLE_PAGE&utm_content=DOCS) - but I still found these resources lacking, so I have summarised my experiences here.

## Updating terraform-created resources based on manual Changes

There are two basic approaches here.
1. Update the terraform state (`terraform.tfstate` file) and take the configs out of there. This is refreshed automatically after `terraform plan` but you can also do it explictly by calling `terraform refresh`.
2. Run `terraform plan`, review the discrepancies and manually transfer these into config

### Example

An example resource with my manual edit 'drift'
![Terraform plan showing edits]({{ "assets/images/terraform-exercise-api-archiver/terraform-plan-s3.png" | relative_url}})

With either approach, if you've updated the config correctly, executing `terraform plan` will eventually show no changes. You now have your infrastructure config caught up to your environment.

![Terraform plan showing no changes]({{ "assets/images/terraform-exercise-api-archiver/terraform-plan-s3-rebase.png" | relative_url}})

I prefer the second approach, mainly because `terraform.tfstate` has all the values explicitly set, including post-creation attributes, and attributes set to defaults - whereas I prefer keeping things minimal.

Once done, I found it handy to use `git diff` to compare the plan output and the config file changes I had made.
{: .notice--info}

![git diff after changes]({{ "assets/images/terraform-exercise-api-archiver/terraform-plan-s3-rebase-git.png" | relative_url}})

## Importing resources, that were *created* manually

![Terraform import workflow]({{ "assets/images/terraform-exercise-api-archiver/terraform-import-workflow-diagram.png" | relative_url}})

The first approach works only when terraform created those resources. 

Terraform intentionally only controls resources that it created, and leaves the rest alone. The `terraform import` command allows us to explicitly tell terraform to manage a resource it did not create. Unfortunately this command still does not give any updates into our config files, and somewhat awkwardly, we in fact need to update the config file with an empty placeholder before we can actually *do* the import.

### Example

![IAM User in AWS console]({{ "assets/images/terraform-exercise-api-archiver/terraform-console-iam.png" | relative_url}})

In my case I wanted to import an IAM user I had created manually. I'd wanted to be cautious on permissions etc so had done this via the AWS console to start with, but now wanted to import back into terraform.

I knew I had a user with permissions and an API key. I'd also created a 'console login profile' I no longer needed.

#### Step 1
I needed to create an empty config placeholder before importing the user. I had to refer to [the docs](https://registry.terraform.io/providers/hashicorp/aws/latest/docs/resources/iam_user) to confirm the resource type. Note at this poing the resource naming in our config could be whatever we like, but I chose to make it match the iam user's name

```terraform
resource "aws_iam_user" "console-archiver" {}
```

#### Step 2
After we have the placeholder, we can then import the state for this object as shown
![Succesful terraform import]({{ "assets/images/terraform-exercise-api-archiver/terraform-import-result.png" | relative_url}})

#### Step 3
To populate the missing config (manually) you can again refer to `terraform plan` output which will callout missing mandatory attributes or highlight any changes from non-default values. The mandatory values can be looked up from tfstate e.g. via `terraform state show aws_iam_user.console-archiver` . Below is an example from another resource after I had imported, but before I had updated the config:
![Terraform plan after import]({{ "assets/images/terraform-exercise-api-archiver/terraform-plan-after-import.png" | relative_url}})

#### Done

After doing this for each of the resources and getting the plans to match the infrastructure, I had a nice and streamlined bit of terraform.
![Terraform config in git diff]({{ "assets/images/terraform-exercise-api-archiver/terraform-new-diff.png" | relative_url}})

{% assign terraform_file = site.pages | where: "name", "media-archive-tf.md" %}

My final terraform file can be seen [here]({{ terraform_file[0].url }})

## Conclusion

It seems the `terraform import` workflow is workable, but not as automated and straightfoward a user experience as I had expected.

After having captured the infrastructure above I was then able to easily do the following 3 steps quite easily:
1. Delete the user and recreate under a new name (api-archiver)
2. Take the new secret out of tfstate* and update where it was needed (ODrive)
3. Disable the key as its no longer needed

As well as the learning journey, I now feel more in control of terraform and my infrastructure.

**Danger Notice:** * As mentioned above, without special attention terraform can download secrets into tfstate, and therefore you should secure tfstate or tfstate.backup very carefully.
{: .notice--danger}
