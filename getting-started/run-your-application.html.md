---
title: "Run your application on infrastructure"
previous: "getting-started/deploy-fully-configured-infrastructure"
next: "getting-started/transition-to-atlas"
---
## Configure a Vagrantfile to push to Atlas
[In the previous step, we deployed fully configured infrastructure on AWS](/help/getting-started/deploy-fully-configured-infrastructure). Now we'll learn how to deploy your application on that infrastructure. 

[Vagrant](https://vagrantup.com) is a cross-platform tool to create and configure lightweight, reproducible, and portable development environments. The template to configure these environments is called a [Vagrantfile](https://docs.vagrantup.com/v2/vagrantfile/index.html). This tutorial will cover one aspect of a Vagrantfile, specifically the `push` config. A Vagrantfile should already be in your [cloned "getting started" directory](https://github.com/hashicorp/atlas-examples/tree/master/getting-started). If not, simply run `vagrant init` in the `app` folder and one will be generated. 

## Vagrant push
Vagrant is capable of pushing application code in the same directory as your Vagrantfile to a remote such as HashiCorp's Atlas or an FTP server.

	config.push.define "atlas" do |push|
	  push.app = "<username>/example-application"
	end

The `app` configuration option is the name of the application in Atlas. If the application does not exist, it will be created with user confirmation. Make sure the push configuration in your Vagrantfile matches the example above, with your username properly filled in. 

In the same directory as the Vagrantfile, update the index.html file with body "Hello World". Now run `vagrant push` in that directory, and the application code will be packaged and sent to Atlas. If you get the error `No VCS found for path`, make sure the directory has git initiated with `git init` and make one commit. You will see your application in the [Development tab](https://atlas.hashicorp.com/development) of your Atlas dashboard:

![Development Dashboard](/help-images/application.png)

## Link the application to images in Atlas
To link your application code to the Packer-made images created in the previous step, [go to the operations tab in Atlas](http://atlas.hashicorp.com/operations). Click on the example-build-configuration, then "linked applications" in the left navigation. Complete the fields with your username, example-application as the application name, and /app as the path. This path tells Packer where to place your application's code, which can referenced later for provisioning or other uses. 

## Update the build configuration to package the application code

Now that the application is stored in Atlas, we can reference the data in the build configuration. Your build configuration should now look as below:

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
	        "type": "file",
	        "source": "/packer/app",
	        "destination": "/tmp"

	    },
	    {
	        "type": "shell",
	        "inline": [
	            "sleep 30",
	            "sudo apt-get update",
	            "sudo apt-get install apache2 -y",
	            "sudo mv /tmp/app/* /var/www/"
	        ]
	    }],
	    "post-processors": [
	      {
	        "type": "atlas",
	        "artifact": "<username>/atlas-example",
	        "artifact_type": "aws.ami",
	        "metadata": {
	          "created_at": "{{timestamp}}"
	        }
	      }
	    ]
	}

The new `file` provisioner takes the application code stored in Atlas and packages it with the artifact output of the build configuration. In the `shell` provisioner the line `sudo mv /tmp/app/* /var/www/` was added to move the application code to Apache's web root. Now run `packer push` in the directory with example-template.json to rebuild the artifacts with your application code merged in. 

	$ packer push example-template.json
	Push successful to 'hashicorp/example-build-configuration'

Notice in your Atlas dashboard that a new build configuration version is made. 

## Deploy your application!
Now that your application is linked to the images, anytime you run `vagrant push`, the images will be rebuilt with the latest version of the application code. 

	$ vagrant push
	Uploaded hashicorp/example-application v2

Wait for the new builds to finish, then just run `terraform apply` in the directory with example-infrastructure.tf to deploy fully configured infrastructure running your application! Terraform will always deploy the latest version of the artifact unless you hardcode the version in the artifact resource block of the Terraform configuration. 

	$ terraform apply
	...

If the apply fails, be sure to first apply the artifact first, then the full infrastructure after. To view the details of the deploy, use `terraform show`. 

	$ terraform show
	...
	aws_elb.web:
	  id = terraform-example-elb
	  availability_zones.# = 2
	  availability_zones.0 = us-east-1d
	  availability_zones.1 = us-east-1d
	  dns_name = terraform-example-elb-1366230309.us-east-1.elb.amazonaws.co7

Now when you go to the public_dns of the load balancer, you'll be able to interact with your application! It may take a minute or two for the instances to pass health checks and register with the load balancer. If you want to bring down your infrastructure, simply run `terraform destroy` and the instances will be terminated. 

## How to start transitioning your infrastructure to Atlas
Now that we've completed the walkthrough, [learn how to transition your own infrastructure and application delivery to Atlas](/help/getting-started/transition-to-atlas)! 