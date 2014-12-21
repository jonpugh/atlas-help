---
title: "Troubleshooting Vagrant Share"
---

# Troubleshooting Vagrant Share

There are several things to check if Vagrant Share isn't working
as expected.

- Is your webserver listening on a specific host, like `website.dev`? For
Vagrant Share to work, it has to be listening on `0.0.0.0`, otherwise
your webserver will not know how to route the domain generated

- Are your assets using absolute URLs, like `127.0.0.1/asset.png`? They'll
need to be relative, i.e `/asset.png`, to be served properly

