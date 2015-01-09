---
title: "Layout infrastructure with a Terraform configuration"
next: "getting-started/package-services-with-artifacts"
---
[After reading how the pieces of Atlas fit together](/help/getting-started/getting-started-overview), it’s time to dig in and understand the constituent parts themselves and how to build them.

## Layout infrastructure with a Terraform configuration:
A Terraform configuration is a holistic view of your infrastructure — the desired type of servers, VMs, or containers running and the respective services on each machine. This section will explain how to write a Terraform configuration to launch two AWS EC2 instances and a load balancer.

The format of Terraform configuration files is
[documented here](https://terraform.io/docs/configuration/index.html).

Open the file `example-infrastructure.tf` from the [example repo](https://github.com/hashicorp/atlas-examples/tree/master/getting-started). The entire configuration is shown below. Verify that there are no other `*.tf` files in your directory, since Terraform loads all of them.

	provider "atlas" {
		token = "ATLAS_TOKEN_HERE"
	}

	provider "aws" {
		access_key = "ACCESS_KEY_HERE"
		secret_key = "SECRET_KEY_HERE"
		region = "us-east-1"
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


	resource "aws_instance" "web" {
	  	instance_type = "t1.micro"
	  	ami = "ami-408c7f28"

	  	# This will create 2 instances
	  	count = 2
	}

If you haven't already, [create an Atlas account](http://atlas.hashicorp.com/account/new) and generate an Atlas token in your [account settings](https://atlas.hashicorp.com/account/tokens). By creating an account, you can view the history of your infrastructure states, manage artifacts, and monitor your build and deploy process.

Replace the `ACCESS_KEY_HERE` and `SECRET_KEY_HERE` with your
AWS access key and secret key, available from
<a href="https://console.aws.amazon.com/iam/home?#security_credential">this page</a>.
We're hardcoding them for now, but you can extract them into [input variables](https://www.terraform.io/intro/getting-started/variables.html) later.

This is a complete configuration that Terraform is ready to apply.
The general structure should be intuitive and straightforward.

The `provider` block is used to configure the named provider, in
our case "aws." A provider is responsible for creating and
managing resources. Multiple provider blocks can exist if a
Terraform configuration is comprised of multiple providers,
which is a common situation.

The `resource` block defines a resource that exists within
the infrastructure. A resource might be a physical component such
as an EC2 instance, or it can be a logical resource such as
a Heroku application.

The resource block has two strings before opening the block:
the resource type and the resource name. In our example, we have one resource type "aws_elb" and the name is "web". The second resource type is "aws\_instance" and the name is "web".
The prefix of the type maps to the provider. In our case
"aws\_instance" automatically tells Terraform that it is
managed by the "aws" provider.

**Note:** For simplicity, we setup the AWS load balancer over HTTP. However, we recommend HTTPS in production environments.

Within the resource block itself is configuration for that
resource. This is dependent on each resource provider and
is fully documented within our
[providers reference](https://terraform.io/docs/providers/index.html). For our EC2 instance, we specify
an AMI for Ubuntu, and request a "t1.micro" instance so we
qualify under the free tier.

## Install Terraform

To install Terraform, find the [appropriate package](https://www.terraform.io/downloads.html) for
your system and download it. Terraform is packaged as a zip archive.

After downloading Terraform, unzip the package into a directory where
Terraform will be installed. On Unix systems, ~/terraform or /usr/local/terraform
is generally good, depending on whether you want to restrict the install to just
your user or install it system-wide. On Windows systems, you can put it wherever you'd like.
The directory will contain a set of binary
programs, such as `terraform`, `terraform-provider-aws`, etc. The final
step is to make sure the directory you installed Terraform to is on the
PATH. See
[this page](http://stackoverflow.com/questions/14637979/how-to-permanently-set-path-on-linux)
for instructions on setting the PATH on Linux and Mac.
[This page](http://stackoverflow.com/questions/1618280/where-can-i-set-path-to-make-exe-on-windows)
contains instructions for setting the PATH on Windows.

## Verifying the Installation

After installing Terraform, verify the installation worked by opening a new
terminal session and checking that `terraform` is available. By executing
`terraform` you should see help output similar to that below:


	$ terraform
	usage: terraform [--version] [--help] <command> [<args>]

	Available commands are:
	    apply      Builds or changes infrastructure
	    graph      Create a visual graph of Terraform resources
	    output     Read an output from a state file
	    plan       Generate and show an execution plan
	    refresh    Update local state file against real resources
	    show       Inspect Terraform state or plan
	    version    Prints the Terraform version


If you get an error that `terraform` could not be found, then your PATH
environment variable was not setup properly. Please go back and ensure
that your PATH variable contains the directory where Terraform was installed.

Otherwise, Terraform is installed and ready to go!

## Setup your machine to communicate with Atlas

In order to communicate with Atlas, you must set an environment variable. You can find your Atlas token in the [account page](/settings/tokens). Then save your Atlas token as an environment variable in your `.bashrc` file. Once it's saved, open a new terminal session.

	export ATLAS_TOKEN=<your_token>

## Link Terraform to Atlas

To save the infrastructure state in Atlas, setup a remote.

	$ terraform remote -name=<your_username>/example

Now when you run Terraform, the infrastructure state will be saved in Atlas. This keeps a versioned history of your infrastructure. If you get an error message, make sure the username you specify on the command line matches the username you created in Atlas. Usernames are case sensitive so be sure to match your username exactly.

## Execution Plan

Next, let's see what Terraform would do when asked to
apply this configuration. In the same directory as the
`example-infrastructure.tf` file, run `terraform plan`. If you cloned the [example repo](https://github.com/hashicorp/atlas-examples/tree/master/getting-started), you should run `terraform plan` in the ops directory. The output will be similar to what is copied below. We've
truncated some of the output to save space.


	$ terraform plan
	...

	+ aws_elb.web
	    availability_zones.#:          "" => "<computed>"
	    dns_name:                      "" => "<computed>"
	    ...

	+ aws_instance.web.0
		ami:               "" => "ami-408c7f28"
	    availability_zone: "" => "<computed>"
	    ...

	+ aws_instance.web.1
		ami:               "" => "ami-408c7f28"
	    availability_zone: "" => "<computed>"
	    ...

`terraform plan` shows what changes Terraform will apply to
your infrastructure given the current state of your infrastructure
as well as the current contents of your configuration.

If `terraform plan` failed with an error, read the error message
and fix the error that occurred. At this stage, it is probably a
syntax error in the configuration, or running `terraform plan` in the wrong directory.

The output format is similar to the diff format generated by tools
such as Git. The output has a "+" next to "aws\_instance.web",
meaning that Terraform will create this resource. Beneath that,
it shows the attributes that will be set. When the value it is
going to is `<computed>`, it means that the value won't be known
until the resource is created.

## Apply

The plan looks good, the configuration appears valid, so it's time to
create real resources. Run `terraform apply` in the same directory
as your `example-infrastructure.tf`, and watch it go! It will take a few minutes
since Terraform waits for the EC2 instance to become available.

	$ terraform apply
	aws_instance.example: Creating...
	  ami:           "" => "ami-408c7f28"
	  instance_type: "" => "t1.micro"

	Apply complete! Resources: 2 added, 0 changed, 0 destroyed.

	...

Done! You can go to the AWS console to prove to yourself that the
EC2 instances have been created.

You can also view Terraform state information in your [Atlas dashboard](http://atlas.hashicorp.com/runtime). Below is an example screenshot:

![Terraform State Screenshot](/help-images/example-terraform-state.png)

## Next

The EC2 instances we launched at this point are based on the AMI
given, but has no additional software installed. [Now let's create an image that defines a web server, so Terraform can then deploy it](/help/getting-started/package-services-with-artifacts).
