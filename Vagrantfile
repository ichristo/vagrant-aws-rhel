require 'yaml'

# Load sensitive AWS credentials from external file
if File.exist?("aws.yml")
  aws_config = YAML.load_file('aws.yml')["aws"]
  pemfile = aws_config["pemfile"]
end

Vagrant.require_plugin "vagrant-aws"

Vagrant.configure("2") do |config|
  
  config.vm.define :broker do |broker_config|
    broker_config.vm.provider :aws do |aws, override|
      override.vm.box = "aws-rhel"
      override.ssh.private_key_path = pemfile
      override.ssh.username         = "ec2-user"

      aws.user_data = aws_config["user_data"]
      aws.access_key_id = aws_config["access_key_id"]
      aws.secret_access_key = aws_config["secret_access_key"]
      aws.keypair_name = aws_config["keypair_name"]
      aws.security_groups = aws_config["security_groups"]
      aws.region = aws_config["region"]
      aws.ami = aws_config["ami"]
      aws.instance_ready_timeout = 120
      aws.instance_type = aws_config["instance_type"]
      aws.tags = {
        'Name' => 'ose-broker-01'
      }
    end
    
    #broker_config.vm.provision :blah do |blah|
    
    #end
  end
  
  config.vm.define :node01 do |node01_config|
    node01_config.vm.provider :aws do |aws, override|
      override.vm.box = "aws-rhel"
      override.ssh.private_key_path = pemfile
      override.ssh.username         = "ec2-user"

      aws.user_data = aws_config["user_data"]
      aws.access_key_id = aws_config["access_key_id"]
      aws.secret_access_key = aws_config["secret_access_key"]
      aws.keypair_name = aws_config["keypair_name"]
      aws.security_groups = aws_config["security_groups"]
      aws.region = aws_config["region"]
      aws.ami = aws_config["ami"]
      aws.instance_ready_timeout = 120
      aws.instance_type = aws_config["instance_type"]
      aws.tags = {
        'Name' => 'ose-node-01'
      }
    end
    
    #broker_config.vm.provision :blah do |blah|
    
    #end
  end

  config.vm.define :node02 do |node02_config|
    node02_config.vm.provider :aws do |aws, override|
      override.vm.box = "aws-rhel"
      override.ssh.private_key_path = pemfile
      override.ssh.username         = "ec2-user"

      aws.user_data = aws_config["user_data"]
      aws.access_key_id = aws_config["access_key_id"]
      aws.secret_access_key = aws_config["secret_access_key"]
      aws.keypair_name = aws_config["keypair_name"]
      aws.security_groups = aws_config["security_groups"]
      aws.region = aws_config["region"]
      aws.ami = aws_config["ami"]
      aws.instance_ready_timeout = 120
      aws.instance_type = aws_config["instance_type"]
      aws.tags = {
        'Name' => 'ose-node-02'
      }
    end
    
    #broker_config.vm.provision :blah do |blah|
    
    #end
  end

  
end
