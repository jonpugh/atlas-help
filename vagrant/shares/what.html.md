---
title: "What are Shares?"
---
# What are Shares?

Shares let you share access to a running Vagrant environment with anyone,
on any network connected to the internet.

There are several different types of shares.

### HTTP Sharing

By default, Vagrant Share shares HTTP access to your Vagrant environment
to anyone in the world. The URL that it creates is publicly accessible
and doesn't require Vagrant to be installed to access -- just a web browser.

    $ vagrant share
    ==> default: Local HTTP port: 5000
        default: Local HTTPS port: disabled
    ==> default: Your Vagrant Share is running!
    ==> default: URL: http://frosty-weasel-0857.vagrantshare.com
    ...

Once the share is created, a relatively obscure URL is outputted. This URL
will route directly to your Vagrant environment; it doesn't matter if you
or accessing party is behing a firewall or NAT.

Currently, HTTP access is restricted through obscure URLs. We'll be adding
more ACLs and audit logs for this in the future.

### SSH Access

While sharing your local webserver is a powerful collaboration tool,
Vagrant Share doesn't stop there. With just a single flag, Vagrant Share
can allow anyone to easily SSH into your Vagrant environment.

Perhaps you're having issues where your app isn't running properly or you
just want to pair program. Now, with just one flag, anyone you want can
SSH into your Vagrant environment from anywhere in the world.

SSH access isn't shared by default. To enable sharing SSH, you must add
the `--ssh` flag to `vagrant share`:

    $ vagrant share --ssh
    ==> default: SSH Port: 22
    ==> default: Generating new SSH key...
        default: Please enter a password to encrypt the key:
        default: Repeat the password to confirm:
        default: Inserting generated SSH key into machine...
    ==> default: Checking authentication and authorization...
    ==> default: Creating Vagrant Share session...
        default: Share will be at: awful-squirrel-9454
    ==> default: Your Vagrant Share is running!
    ...

When the `--ssh` flag is provided, Vagrant generates a brand new SSH keypair
for SSH access. The public key portion is automatically inserted into the
Vagrant environment. The private key portion is uploaded to the server
managing the Vagrant Share connections. The password used to encrypt the
private key is _not_ uploaded anywhere, however, meaning we couldn't access
your VM if we wanted to. It is an extra layer of security.

Once SSH access is shared, the person wanting to access your Vagrant
environment uses `vagrant connect` to SSH in:

    $ vagrant connect --ssh awful-squirrel-9454
    Loading share 'awful-squirrel-9454'...
    Password for the private key:
    Executing SSH...

    Welcome to Ubuntu 12.04.1 LTS

     * Documentation:  https://help.ubuntu.com/
    Last login: Wed Feb 26 08:38:55 2014 from 192.168.148.1
    vagrant@precise64:/vagrant$


The name of the share and the password used to encrypt the private key
must be communicated to the other person manually, as a security measure.

### Vagrant Connect

Vagrant share can share any TCP/UDP connection, and is not restricted
to only a single port. When you run `vagrant share`, Vagrant will share
the entire Vagrant environment.

When the person you are sharing with runs `vagrant connect SHARE-NAME`,
Vagrant will give this person a static IP they can use to access the
machine as if it were on the local network:


    $ vagrant connect awful-squirrel-9454
    ==> connect: Connecting to: awful-squirrel-9454
    ==> connect: Starting a VM for a static connect IP.
        connect: The machine is booted and ready!
    ==> connect: Connect is running!
    ==> connect: SOCKS address: 127.0.0.1:62167
    ==> connect: Machine IP: 172.16.0.2
    ==> connect:
    ==> connect: Press Ctrl-C to stop connection.
    ...

### Security Concerns

Sharing your Vagrant environment understandably raises a number of security
issues.

The primary security mechanism for Vagrant Share is security through obscurity
along with an encryption key for SSH. Additionally, there are several
configuration options made available to help control access and manage security:

  * `--disable-http` will not create a publicly accessible HTTP URL. When
    this is set, the only way to access the share is with `vagrant connect`.

  * `--ssh-once` will allow only one person to SSH into your shared environment.
    After the first SSH access, the keypair is physically deleted and SSH
    access won't be possible anymore.

In addition to these options, there are other features we've built to help:

  * Vagrant share uses end-to-end TLS connections. So even unencrypted TCP streams
    are encrypted through the various proxies and only unencrypted during the final
    local communication between the local proxy and the Vagrant environment.

  * SSH keys are encrypted by default, using a password that is not transmitted
    to our servers or across the network at all.

  * SSH is not shared by default, it must explicitly be shared with the
    `--ssh` flag.

  * A web interface we've built shows share history and will show basic
    access logs in the future.

  * Share sessions expire after a short time (currently 8 hours), but
    can also be expired manually by `ctrl-c` from the sharing machine
    or via the web interface.

Most importantly, you must understand that by running `vagrant share`,
you are making your Vagrant environment accessible by anyone who knows
the share name. When share is not running, it is not accessible.
