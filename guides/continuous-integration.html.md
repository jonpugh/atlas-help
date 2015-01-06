---
title: "Using Atlas with Continuous Integration"
---
## Using Atlas with continuous integration
![How Atlas works](/help-images/how-atlas-works.png)

Continuous integration tools like Jenkins or CircleCI are simple to integrate into the Atlas application delivery pipeline. You can configure `vagrant push` to [run a script locally](https://docs.vagrantup.com/v2/push/local-exec.html) which kicks off a test in your CI tool. 

	config.push.define "local-exec" do |push|
	  	push.inline = <<-SCRIPT
	    	scp . /var/www/website
	  	SCRIPT
	end

Once the tests pass in your CI tool, pass the application slug back to Atlas using the [Atlas strategy](https://docs.vagrantup.com/v2/push/atlas.html) for `vagrant push`.

	config.push.define "atlas" do |push|
	    push.app = "username/application"
	end

"App" is the name of the application to upload to within Atlas. For example, "hashicorp/bar".
