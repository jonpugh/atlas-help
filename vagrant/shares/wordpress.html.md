---
title: "Using Vagrant Share with Wordpress"
---

# Using Vagrant Share with Wordpress

By default, Wordpress uses absolute URLs. This means
that theme HTML may contain `http://127.0.0.1/asset.png` instead
of `/asset.png`.

Whoever is visiting your share via the public URL unfortunately can't
route to `127.0.0.1` as an address. However, if the HTML contained
`/asset.png`, the browser would automatically assume it's on the same
host as the Vagrant Share URL, which it can access.

To enable this, you can use the [Root Relative URLs](http://wordpress.org/plugins/root-relative-urls/)
plugin. Vagrant Share should then work as expected!

