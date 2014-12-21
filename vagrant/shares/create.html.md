---
title: "How to Create a Share"
---

# How to Create a Share

You've learned [what](/help/shares/what) shares are and want to try
it out.

*Heads up! You'll need [Vagrant 1.5](/help/cloud/vagrant-1-5) installed
to use this feature.*

## HTTP

1. Run `vagrant login` and enter your Atlas by HashiCorp details
2. Boot a Vagrant environment and verify it's running an HTTP server
3. Run `vagrant share`. You're done!

## SSH

1. Run `vagrant login` and enter your Atlas by HashiCorp details
2. Boot a Vagrant environment
3. Run `vagrant share --ssh`
4. Enter a password for encrypting the SSH key
5. Give your Share name and the password to a friend and have them
run `vagrant connect --ssh [SHARE NAME]`

## Connect

1. Run `vagrant login` and enter your Atlas by HashiCorp details
2. Boot a Vagrant environment
3. Run `vagrant share --disable-http`
4. Give your Share name to a friend and have them run `vagrant connect [SHARE NAME]`
