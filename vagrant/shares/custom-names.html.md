---
title: "How to Use Custom Share Names"
---

# How to Use Custom Share Names

*Heads up! You'll need to be on the Pro or Organization plan to use this feature.*

## Steps

1. Run Vagrant share with the `--name` flag, specifiying the name you
want to use
2. The share should be created with that name as the leading subdomain
3. Share names are recycled when they expire automatically, meaning they
can be used again (by any user, if not on a custom domain)
4. If a share doesn't expire correctly, you can visit the [shares page](/shares)
and manually recycle the name by clicking the "Recycle name" link

Notes:

- You can use custom names with custom domains. Uniqueness will be verified
within the scope of the domain
- Anyone can access. We recommend you use hard to guess names on the
public cloud, `vagrantshare.com`
