---
title: "Using Atlas with Configuration Management Tools"
---
# Using Atlas with configuration management tools
Atlas works with existing configuration management in one of two ways. First, teams can use their existing configuration scripts to build machine images which are then deployed with Atlas. Or, using provisioners, Atlas enables any configuration management tool to be used to setup a resource once it has been created.

More information about using configuration management to build images can be found in the [Packer documentation for building images](http://www.packer.io/docs/basics/terminology.html#Provisioners).

More information about using configuration management to setup a resource after it has been created can be found in the [Terraform documentation for provisioners](https://www.terraform.io/docs/provisioners/index.html).   