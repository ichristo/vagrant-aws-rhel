require 'yaml'

# Load sensitive AWS credentials from external file
if File.exist?("aws.yml")
  aws_config = YAML.load_file('aws.yml')["aws"]
  pemfile = aws_config["pemfile"]
end

Vagrant.configure("2") do |config|
  config.vm.hostname = "ose-broker"
  config.vm.box = "dummy"
  config.vm.box_url = "https://github.com/mitchellh/vagrant-aws/raw/master/dummy.box"
  config.vm.boot_timeout   = 120

  config.vm.provider :aws do |aws, override|
    aws.access_key_id = aws_config["access_key_id"]
    aws.secret_access_key = aws_config["secret_access_key"]
    aws.keypair_name = aws_config["keypair_name"]
    aws.security_groups = aws_config["security_groups"]
    aws.region = aws_config["region"]
#    aws.security_groups = ENV['AWS_SECURITY_GROUP']

    aws.instance_ready_timeout = 120
    aws.instance_type = aws_config["instance_type"]
    aws.ami = aws_config["ami"]
    
    override.ssh.username         = "ec2-user"
    override.ssh.private_key_path = pemfile
    
    aws.tags = {
      'Name' => 'ose-broker',
    }
    
  end
end
