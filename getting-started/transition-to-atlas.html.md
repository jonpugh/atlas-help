---
title: "Transitioning your infrastructure to Atlas"
previous: "getting-started/run-your-application"
---
## Transitioning your infrastructure to Atlas
In this tutorial we went through 3 steps to deploy an application and infrastructure with Atlas: 

_1. Layout and deploy infrastructure with Terraform_
<br>
_2. Define services with Packer-made artifacts_
<br>
_3. Configure vagrant push to send application code to Atlas_
<br>

To transition your current application and infrastructure to Atlas, we recommend you follow the same steps as the tutorial, but for just one service at a time. First, list all current running services; for example web servers, database, task workers, etc. Then choose just one service to transition to Atlas. Layout the necessary infrastructure in a Terraform configuration, define the software requirements for the service in a Packer template, and finally create or update your Vagrantfile to include the `push` configuration. After the first service is fully managed by Atlas, it's simple to quickly transition all of your services. If you have any questions specific to your infrastructure, we're always here to help at [support@hashicorp.com](mailto:support@hashicorp.com).

## Atlas integrates with your existing application delivery pipeline
Atlas works whether you're building an application delivery pipeline from scratch or already have existing pieces. For example, if you're using configuration management to configure software on servers, you can use the same recipes or scripts to create Packer images. To read more about using configuration scripts to build images, [read here](http://www.packer.io/docs/basics/terminology.html#Provisioners). This is just one example of how to use Atlas with existing tools. For additional examples, view our full [guides section](/help/guides/continuous-integration) or read through [use cases](/help/use-cases/adopt-devops).
