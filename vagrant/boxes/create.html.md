---
title: "Creating a New Box"
---
# Creating a New Box

Atlas by HashiCorp is made for sharing boxes. This page will cover creating
a new box on Atlas by HashiCorp. It does not cover creating the actual physical
"box" file. For that, please read the
[Vagrant documentation on creating base boxes](http://docs.vagrantup.com/v2/boxes/base.html).

Once you've created the physical box file, adding it to Atlas by HashiCorp
for use with Vagrant is simple.

1. Sign up and log in with an account to Atlas by HashiCorp, if you haven't
   already.

1. Click [Create Box](/boxes/new) in the upper right corner.

1. Follow the instructions for creating a box that are shown. You'll need
   to create a box, create a version for that box, then add at least one
   provider to that box. Follow the forms and they'll guide you through it.

1. After adding a provider, click the "Release Now" button to release the
   box. This will make it available for Vagrant.

Once you've created and released a box, you can release new versions of
the box by clicking "Create New Version" under the versions dropdown of
a box page.

For more information on the release lifecycle of boxes, see
the [help page dedicated to it](/help/boxes/lifecycle).
