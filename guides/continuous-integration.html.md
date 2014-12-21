---
title: "Using Atlas with Continuous Integration"
---
## Using Atlas with continuous integration
![How Atlas works](/help-images/how-atlas-works.png)

Continuous integration tools like Jenkins or CircleCI are simple to integrate into the Atlas application delivery pipeline. You can configure `vagrant push` to run a script locally which kicks off a test in your CI tool. 

	config.push.define "local-exec" do |push|
	  	push.inline = <<-SCRIPT
	    	scp . /var/www/website
	  	SCRIPT
	end

Once the tests pass in your CI tool, pass the application slug back to Atlas:

	$ atlas app path

"App" is the name of the application to upload to within Atlas.
If path is a directory, it will be compressed (gzip tar) and uploaded
in its entirety. The root of the archive will be the path. For clarity:
if you upload the "foo/" directory, then the file "foo/version" will be
"version" in the archive since "foo/" is the root. 