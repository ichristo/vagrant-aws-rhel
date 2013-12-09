vagrant-aws-rhel
================
##Prerequisites

###Install Homebrew
If you're a Linux user, then you're familiar with using package managers
to install software packages.  This is clearly one of the missing
features for OS X and there have been several attempts out there to fill
the gap.  

Created by [Max Howell](http://mxcl.github.io/),
[Homebrew](http://brew.sh/) is one of the package management options
that we'll use for installing the necessary packages on OS X.  

To install [Homebrew](http://brew.sh/), simple run the install script
via curl and ruby:

    ruby -e "$(curl -fsSL
https://raw.github.com/mxcl/homebrew/go/install)"

The script explains what it will do and then pauses before it does it. 

###Install Brew-Cask
Yes, "clicking and dragging " an icon via Mac OS DMG-style packages is
incredible simple.  But what if we could download and install software
via the command-line.  Or even better, run a script to install AND
configure the application in one simple step.

Well, through the magic of Open Source, there's a tool for that called
[brew-cask](https://github.com/phinze/homebrew-cask). This tool, written
by [Paul Hinze](http://phinze.com) provides a simple
[CLI](http://en.wikipedia.org/wiki/Command-line_interface) workflow for
the administration of Mac applications distributed as binaries.

To install it, simply install it via Homebrew:

    $ brew tap phinze/homebrew-cask
    $ brew install brew-cask


### Install Vagrant
[Vagrant](www.vagrantup.com) is a tool written by [Mitchell
Hashimoto](http://mitchellh.com/) to streamline the process of
provisioning virtual instances. By using vagrant, developers can rapidly
create an isolated environment to deploy and test applications on their
local machines.

Originially, Vagrant only supported VirtualBox, put today extensions
exist for AWS & VMWare environments as well.  Imagine the value of
deploying an application the same way in Amazon's EC2 that you do on
your local machine ... all within a few minutes.

You can download the installation package from the [Vagrant Download
page](http://downloads.vagrantup.com/). If you're using Homebrew and
Casks, its as simple as this:

    $ brew cask install vagrant
    
    ==> Downloading
http://files.vagrantup.com/packages/a40522f5fabccb9ddabad03d836e12
    ==> Running installer for vagrant; your password may be necessary.
    ==> installer: Package name is Vagrant
    ==> installer: Upgrading at base path /
    ==> installer: The upgrade was successful.
    ==> Success! vagrant installed to
/opt/homebrew-cask/Caskroom/vagrant/1.3.5
    
    
    
### Install the Vagrant AWS Plugin
Vagrant can be used to manage EC2 and VPC instances via the [vagrant-aws
plugin](https://github.com/mitchellh/vagrant-aws).  

After installing this plugin, it will be possible to do the following
via vagrant:

* Boot EC2 or VPC instances.
* SSH into the instances.
* Provision the instances with any built-in Vagrant provisioner.
* Minimal synced folder support via rsync.
* Define region-specifc configurations so Vagrant can manage machines in
  multiple regions.

To install the Vagrant AWS Plugin, simply do the following:

    $ vagrant plugin install vagrant-aws
    
    Installing the 'vagrant-aws' plugin. This can take a few
     minutes...
    Installed the plugin 'vagrant-aws (0.4.0)'!

## Creating the AWS Instances
### Configure AWS Account
Copy the aws.example.yml in the config directory to aws.yml.
Modify the settings with the correct AWS settings:

    aws:
      ami: 'ami-a25415cb'   #RHEL 6 AMI
      subnet_id: '{SUBNET FROM THE VPC}'
      access_key_id     : '{YOUR_ACCESS_KEY}YOUR_ACCESS_KEY'
      secret_access_key : '{YOUR_SECRETACCESS_KEY}'
      keypair_name      : '{YOUR_KEYPAIR_NAME}'
      security_groups   : [ 'default' ]
      region: 'us-east-1'region
      pemfile: '{YOUR_PEM_FILE}'
      user_data: "#!/bin/bash\necho 'Defaults:ec2-user !requiretty' >
/etc/sudoers.d/999-vagrant-cloud-init-requiretty && chmod 440
/etc/sudoers.d/999-vagrant-cloud-init-requiretty\n"   #NECESSARY FOR Red
Hat flavors of Linux (Amazon, Centos, Fedora, RHEL)

In the Vagrantfile, Verify that the hostnames and number of boxes is
correct.

    boxes = [
      { 
        :name           =>  'ose-broker-01',
        :ip             =>  '192.168.200.101',
        :primary        =>  'true',
        :instance_type  =>  't1.micro'
      },
      { 
        :name           =>  'ose-node-01',
        :ip             =>  '192.168.200.121',
        :primary        =>  'false',
        :instance_type  =>  't1.micro'
      },
      { 
        :name           =>  'ose-node-02',
        :ip             =>   '192.168.200.122',
        :primary        =>  'false',
        :instance_type  =>  't1.micro'
      },
      
    ]
    
### Run It!
Run the following from your project root to create the AWS instances:

    $ vagrant up --provider=aws

And there you have it ! 

You can ssh into the primary instance with the following: –

    $ vagrant ssh 

To ssh into the secondary instances:

    $ vagrant ssh ose-node-01
    
To 'TERMINATE' the EC2 instances:

    $ vagrant destroy ose-broker-01 ose-node-01 ose-node-02
