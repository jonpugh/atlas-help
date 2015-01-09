---
title: "Deploy fully configured infrastructure"
previous: "getting-started/package-services-with-artifacts"
next: "getting-started/run-your-application"
---
## Recap
Before we deploy fully configured infrastructure, let's review what has already been done in the [getting started guide](/help/getting-started/getting-started-overview). First, we learned how to layout and deploy basic infrastructure with Terraform. Then we learned how to build configured machine images with Packer. Now we want to combine the two — layout and deploy fully configured infrastructure!

## Terraform configuration
Now that we have an artifact stored in Atlas, we can reference it in the Terraform configuration:

	provider "atlas" {
	    token = "ATLAS_TOKEN_HERE"
	}

	provider "aws" {
	  access_key = "ACCESS_KEY_HERE"
	  secret_key = "SECRET_KEY_HERE"
	    region = "us-east-1"
	}

	resource "atlas_artifact" "web" {
	    name = "<username>/example-artifact"
	    type = "aws.ami"
	}

Make sure your Terraform configuration only has the above information, and run `terraform apply` to create the artifact reference in Terraform. 

	$ terraform apply
	...
	atlas_artifact.web: Creating...
	  file_url:     "" => "<computed>"
	  name:         "" => "hashicorp/example-artifact"
	  slug:         "" => "<computed>"
	  type:         "" => "aws.ami"
	  version_real: "" => "<computed>"
	atlas_artifact.web: Creation complete

	Apply complete! Resources: 1 added, 0 changed, 0 destroyed.

Now, let's combine the artifact with the [Terraform configuration written in the first step](/help/getting-started/layout-infrastructure). 

The complete Terraform configuration should now look as below. 

	provider "atlas" {
	    token = "ATLAS_TOKEN_HERE"
	}

	provider "aws" {
	    access_key = "ACCESS_KEY_HERE"
	    secret_key = "SECRET_KEY_HERE"
	    region = "us-east-1"
	}

	resource "atlas_artifact" "web" {
	    name = "<username>/example-artifact"
	    type = "aws.ami"
	}

	resource "aws_elb" "web" {
	    name = "terraform-example-elb"

	    # The same availability zone as our instances
	    availability_zones = ["${aws_instance.web.*.availability_zone}"]

	    listener {
	        instance_port = 80
	        instance_protocol = "http"
	        lb_port = 80
	        lb_protocol = "http"
	    }

	    # The instances are registered automatically
	    instances = ["${aws_instance.web.*.id}"]
	}

	resource "aws_security_group" "allow_all" {
	  name = "allow_all"
	    description = "Allow all inbound traffic"

	  ingress {
	      from_port = 0
	      to_port = 65535
	      protocol = "tcp"
	      cidr_blocks = ["0.0.0.0/0"]
	  }
	}

	resource "aws_instance" "web" {
	    instance_type = "t1.micro"
	    ami = "${atlas_artifact.web.metadata_full.region-us-east-1}"
	    security_groups = ["${aws_security_group.allow_all.name}"]

	    # This will create 2 instances
	    count = 2
	}


Notice in the `aws_instance` resource now references the artifact stored in Atlas. The web resource has an additional field `security_groups`, which references the security group that allows traffic to access the servers.

Now, when you run `terraform apply`, it will reference the proper AMI stored in Atlas that is configured with Apache.

	$ terraform apply
	...
	aws_security_group.allow_all: Creation complete
	...
	aws_instance.web.0: Creation complete
	...
	aws_instance.web.1: Creation complete
	...
	aws_elb.web: Creation complete

	Apply complete! Resources: 4 added, 0 changed, 0 destroyed.

	...

That's it! You now have a fully functional 2-tier infrastructure running on AWS. Up next we'll learn how to get application code on all of your servers.
