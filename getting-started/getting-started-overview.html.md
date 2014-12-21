---
title: "Overview: Launching 2-tier infrastructure on AWS"
---
## Overview

This tutorial will walk through launching a simple application on 2-tier AWS infrastructure. First, a quick overview of terminology and how the pieces fit together:

## Atlas Hierarchy
![Atlas Hierarchy](/help-images/docs-overview.png)

**Infrastructure** is made up of many **nodes**, which run one or more **services**, such as a database, web servers, or load balancer. A **service** is defined by both it's software requirements, such as Ruby, Redis, Nginx, and the service's application code itself. A service can run across one or many nodes; for example your web server service could run on 12 nodes, while your database service runs on one node. A **node** could be a virtual machine, physical machine, or container — whatever you are using to partition compute resources.

Each piece of Atlas corresponds to an open source project built by [HashiCorp](http://hashicorp.com). **Infrastructure** is layed out in a [Terraform](https://terraform.io) configuration. The **software requirements of a service** are configured with artifacts (AMIs, containers, VMware images, etc) built by [Packer](https://packer.io). The **application code of a service** is defined by a [Vagrantfile](https://vagrantup.com). **Nodes** are monitored for health and services are connected across nodes with [Consul](https://consul.io). These open source projects are production tested with over 500,000 active users.

![OSS to Atlas Feature](/help-images/oss-to-atlas-feature.png)

Atlas encapsulates the full application delivery process from developing application code to the deployment and maintenance of individual nodes:
![How Atlas Works](/help-images/how-atlas-works.png)

After configuring an infrastructure layout with Terraform and defining services' software requirements with Packer, the resulting development workflow is simple. Bring up a development environment with `vagrant up` to develop and test code changes. Once the developer is satisfied with the changes, `vagrant push` then packages the application code and hands it off to Packer to build artifacts with the updated application slug and necessary software requirements. Those artifacts are stored in a Registry, which Terraform references to get the proper artifacts for each node. Terraform then provisions the nodes and deploys the proper artifacts to each node. Once the infrastructure is provisioned, Consul is responsible for monitoring the health and status of all nodes and services.

It's important to note that each of these steps is connected with a public API. So it's simple to integrate Atlas into an existing application delivery processes. Read more about using Atlas with existing services, such as CI, containers, or configuration management in the [guides section](/help/overview#guides).

## Getting Started with Atlas
This walkthrough will show a simple example of deploying an application and configured infrastructure with Atlas. The first step is laying out and deploying basic infrastructure with Terraform. The next step is building configured machine images with Packer. The final step combines the two — layout and deploy fully configured infrastructure!

To get started, create a directory named "atlas-example" with the below folders and files. You can also [clone our example repo here](https://github.com/hashicorp/atlas-examples). 

	atlas-example
	|-- ops
		|-- example-infrastructure.tf
		|-- example-template.json
	|-- app
		|-- index.html
		|-- Vagrantfile

The ops folder contains all the configurations to deploy your application, and the app folder contains the application code and Vagrantfile which pushes the application code to Atlas. Each file and step will be explained in detail, [starting with laying out and deploying infrastucture with Terraform](/help/getting-started/layout-infrastructure).
