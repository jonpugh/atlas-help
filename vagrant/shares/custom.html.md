---
title: "How to Set-up Custom Share Domains"
---

# How to Set-up Custom Share Domains

*Heads up! You'll need to be on the Pro or Organization plan to use this feature.*

## Steps

1. Add the domain you want to use and have adminstrative control over, like
`myvagrantshare.example.com`, to your share settings for either your [personal](/account/share)
upgraded account or one of your [organizations](/organizations)
2. Create a DNS CNAME record for the domain with a leading asterisk, e.g `*.myvagrantshare.example.com` pointing to `share.vagrantcloud.com` (note the leading asterisk)
3. Optionally, set the domain as your default by clicking "set default". This is
a personal account setting and doesn't affect other users in your organization
4. Run Vagrant share. If the default is set, it should just work. Otherwise,
use the `--domain` flag and pass in the domain name

Notes:

- You can use sub-domains and root domains
- Domains have to be unique, so if you want to share them with team members
you'll need to add the domain to the organization
