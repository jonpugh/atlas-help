---
title: "Package Services with Artifacts"
previous: "getting-started/layout-infrastructure"
next: "getting-started/deploy-fully-configured-infrastructure"
---
[In the previous step EC2 instances were launched](/help/getting-started/layout-infrastructure), but with base AMIs without software configured. Now we'll learn how to configure AMIs with the proper software installed and deploy them.

## Package services with machine images

Services are deployed using an artifact which can be created at different granularities.
An artifact could be an ISO for a physical machine, VM image, AWS AMI, or Docker container.
For example you can create AWS AMI for a web service, database, load balancer, etc.
A Packer template defines what software is installed on the machine image.
Below is the Packer template from the [example repo](https://github.com/hashicorp/atlas-examples/tree/master/getting-started) that creates an AMI for a web server with Apache installed:

	{
	    "builders": [{
	        "type": "amazon-ebs",
	        "access_key": "ACCESS_KEY_HERE",
	        "secret_key": "SECRET_KEY_HERE",
	        "region": "us-east-1",
	        "source_ami": "ami-de0d9eb7",
	        "instance_type": "t1.micro",
	        "ssh_username": "ubuntu",
	        "ami_name": "atlas-example {{timestamp}}"
	    }],
	    "push": {
	      "name": "<username>/example-build-configuration"
	    },
	    "provisioners": [
	    {
	        "type": "shell",
	        "inline": [
	            "sleep 30",
	            "sudo apt-get update",
	            "sudo apt-get install apache2 -y"
	        ]
	    }],
	    "post-processors": [
	      {
	        "type": "atlas",
	        "artifact": "<username>/example-artifact",
	        "artifact_type": "aws.ami",
	        "metadata": {
	          "created_at": "{{timestamp}}"
	        }
	      }
	    ]
	}

If you haven't already, create an AWS account to complete the walkthrough. For the example, we'll use a "t1.micro" instance to build our image, which qualifies under the AWS free-tier, meaning it will be free. If you already have an AWS account, you may be charged some amount of money, but it shouldn't be more than a few cents.

Packer can build images for many platforms other than AWS, but AWS requires no additional software installed on your computer and their free-tier makes it free to use for most people. This is why we chose to use AWS for the example.

The access keys and Atlas token are hardcoded in this example, but you can pass them in as
[user variables](https://packer.io/docs/templates/user-variables.html) if preferred
to keep your secret keys out of the template. If you haven't already, [create an Atlas account](https://atlas.hashicorp.com/create) to get a token.

## Builders

	"builders": [{
		"type": "amazon-ebs",
		"access_key" = "ACCESS_KEY_HERE",
		"secret_key" = "SECRET_KEY_HERE",
		"region": "us-east-1",
		"source_ami": "ami-de0d9eb7",
		"instance_type": "t1.micro",
		"ssh_username": "ubuntu",
		"ami_name": "atlas-example {{timestamp}}"
	}]

The `builders` section contains an array of JSON objects configuring a specific _builder_. A
builder is a component of Packer that is responsible for creating a machine
and turning that machine into an image.

In this case, we're only configuring a single builder of type `amazon-ebs`.
This is the Amazon EC2 AMI builder that ships with Packer. This builder
builds an EBS-backed AMI by launching a source AMI, provisioning on top of
that, and re-packaging it into a new AMI.

The additional keys within the object are configuration for this builder, specifying things
such as access keys, the source AMI to build from, and more.
The exact set of configuration variables available for a builder are
specific to each builder and can be found within the [documentation](https://packer.io/docs).

## Push

    "push": {
      "name": "<username>/example-build-configuration"
    }

The `push` section is a dictionary with your build configuration name and Atlas access token. For the name, be sure to replace username with your case-sensitive username. If the build configuration doesn't exist in Atlas, it will be created.

## Provisioners

	"provisioners": [
	{
	    "type": "shell",
	    "inline": [
	        "sleep 30",
	        "sudo apt-get update",
	        "sudo apt-get install apache2 -y"
	    ]
	}]

The provisioners section
is an array of provisioners to run. If multiple provisioners are specified, they
are run in the order given.

By default, each provisioner is run for every builder defined. So if we had
two builders defined in our template, such as both Amazon and DigitalOcean, then
the shell script would run as part of both builds. There are ways to restrict
provisioners to certain builds, but it is outside the scope of this getting
started guide. It is covered in more detail in the complete
[documentation](https://packer.io/docs).

The one provisioner we defined has a type of `shell`. This provisioner
ships with Packer and runs shell scripts on the running machine. In our
case, we specify inline commands to run in order to install Apache.

**Note:** The `sleep 30` in the example above is very important. Because Packer is able to detect and SSH into the instance as soon as SSH is available, Ubuntu actually doesn't get proper amounts of time to initialize. The sleep makes sure that the OS properly initializes. It's also very important to pass `-y` into the install command, otherwise the build will run indefinitely waiting for a user confirmation.

## Managing the image with post-processors

	"post-processors": [
	  {
	    "type": "atlas",
	    "artifact": "<username>/example-artifact",
	    "artifact_type": "aws.ami",
	    "metadata": {
	      "created_at": "{{timestamp}}"
	    }
	  }
	]

Packer builds images and then uses a `post-processor` to store them in Atlas. Just supply your Atlas token in the Packer template and the images will be automatically stored in Atlas for you to manage and deploy. The field `artifact` defines the name that you will later reference to deploy the artifact. Be sure to properly input your case-sensitive username.

## Install Packer

To install packer, first find the [appropriate package](https://packer.io/downloads.html)
for your system and download it. Packer is packaged as a "zip" file.

Next, unzip the downloaded package into a directory where Packer will be
installed. On Unix systems, `~/packer` or `/usr/local/packer` is generally good,
depending on whether you want to restrict the install to just your user
or install it system-wide. On Windows systems, you can put it wherever you'd
like.

After unzipping the package, the directory should contain a set of binary
programs, such as `packer`, `packer-build-amazon-ebs`, etc. The final step
to installation is to make sure the directory you installed Packer to
is on the PATH. See [this page](http://stackoverflow.com/questions/14637979/how-to-permanently-set-path-on-linux)
for instructions on setting the PATH on Linux and Mac.
[This page](http://stackoverflow.com/questions/1618280/where-can-i-set-path-to-make-exe-on-windows)
contains instructions for setting the PATH on Windows.

## Verifying the Installation

After installing Packer, verify the installation worked by opening
a new command prompt or console, and checking that `packer` is available:

	$ packer
	usage: packer [--version] [--help] <command> [<args>]

	Available commands are:
	    build        build image(s) from template
	    fix          fixes templates from old versions of packer
	    inspect      see components of a template
	    validate     check that a template is valid

If you get an error that `packer` could not be found, then your PATH
environmental variable was not setup properly. Please go back and ensure
that your PATH variable contains the directory which has Packer installed.

Otherwise, Packer is installed and you're ready to go!


## Push to Atlas

It is time to build your first image. This is done by calling `packer push` with the template file.


	$ packer push -create example-template.json

This pushes the build configuration to Atlas and automatically starts the build. If `packer push` fails, be sure you properly set your Atlas token as an environment variable in [previous step](/help/getting-started/layout-infrastructure). You can view the real-time status of the build in the Atlas dashboard by clicking the build.

![Packer Build](/help-images/packer-build.png)

If the build fails, read the output logs in the Atlas dashboard to debug the issue. The most common issue for a build erroring is incorrect AWS keys in your Packer template. Once the build is finished, the output is an artifact which you can manage in the [Runtime tab](https://atlas.hashicorp.com/runtime) of the Atlas dashboard.

![Artifact](/help-images/artifact.png)

Congratulations! You've just built your first image with Packer and stored it in Atlas. Now let's deploy the artifact!
