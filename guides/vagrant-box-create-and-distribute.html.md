---
title: "Using Atlas to Store and Distribute Vagrant Boxes"
---
# Using Atlas to store and distribute Vagrant boxes

Atlas by HashiCorp makes it simple to upload and share Vagrant boxes. 
This page will cover creating a new box on Atlas and how to distribute it to users.
It does not cover creating the actual physical "box" file. For that, please read the
[Vagrant documentation on creating base boxes](http://docs.vagrantup.com/v2/boxes/base.html).

Once you've created the physical box file, adding it to Atlas
for use with Vagrant is simple.

1. [Sign up](/account/new) and log in with an account to Atlas, if you haven't
   already.

1. Go to the [Create Box](/boxes/new) page.

1. Follow the instructions for creating a box that are shown. You'll need
   to create a box, create a version for that box, and then add at least one
   provider to that box. Follow the forms and they'll guide you through it. Every
   box on Atlas must have the box file itself, a version, and at least one provider.

1. After adding a provider, click the "Update provider" button to release the
   box. This will make it available for Vagrant. You can find all your boxes
   in the [development tab](/development) of Atlas under the "Boxes" header in the 
   left navigation bar.

Once you've created and released a box, you can release new versions of
the box by clicking "Create New Version" under the versions dropdown of
a box page. For more information on the release lifecycle of boxes, see
the [help page dedicated to it](/help/boxes/lifecycle).

To distribute the box to your team, update your Vagrantfile to reference the
box on Atlas.

	Vagrant.configure(2) do |config|
	  config.vm.box = "<username>/<boxname>"
	end

Now when a team member runs `vagrant up`, the box will be downloaded from Atlas.
If the box is private, the team member will be prompted to authorize access. Users
are granted access to private resources by logging in with an Atlas username and
password or by using a shared Atlas token.
[Learn more about authorization options here](/help/account-management/command-line-login).
