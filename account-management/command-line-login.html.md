---
title: "Command-line Login"
---
## Command-line Login

To access and download private resources from Atlas, such as Vagrant boxes, you must login. You can login with either your Atlas username and password, or with an Atlas token. You can generate a token for use by multiple users in your [organization's account page](/settings/tokens).

Username and password login:

	$ vagrant login
	# ...
	Atlas username:
	Atlas password:

Token login:

	$ vagrant login --token ABCD1234
	The token was successfully saved.

You can read more about `vagrant login` and its options in the [Vagrant documentation](https://docs.vagrantup.com/v2/cli/login.html). 
