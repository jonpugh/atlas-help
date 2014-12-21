---
title: "Intrastructure Management"
---
## Infrastructure Management - CloudFormation, Heat, etc
Tools like CloudFormation, Heat, etc. allow the details of an infrastructure to be codified into a configuration file. The configuration files allow the infrastructure to be elastically created, modified and destroyed.

Atlas similarly uses configuration files to detail the infrastructure setup, but it goes further by being both cloud-agnostic and enabling multiple providers and services to be combined and composed. For example, Atlas can be used to orchestrate an AWS and OpenStack cluster simultaneously, while enabling integration with 3rd-party providers like CloudFlare and DNSimple to provide CDN and DNS services. This enables Atlas to represent and manage an entire infrastructure with its supporting services, instead of only the subset that exists within a single provider. It provides a single unified syntax, instead of requiring operators to use independent and non-interoperable tools for each platform and service.