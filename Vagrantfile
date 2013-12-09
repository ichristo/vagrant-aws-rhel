require 'yaml'

# Load sensitive AWS credentials from external file
if File.exist?("config/aws.yml")
  aws_config  = YAML.load_file("config/aws.yml")["aws"]
  pemfile     = aws_config["pemfile"]
end

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

VAGRANT_API_VERSION = "2"

Vagrant.require_plugin "vagrant-aws"

Vagrant.configure(VAGRANT_API_VERSION) do |config|
  boxes.each do |box|
    config.vm.define box[:name], primary: box[:primary] do |config|
      config.vm.box               = "aws-rhel"
      config.vm.hostname          = box[:name]
      config.vm.boot_timeout      = 120
      
      config.vm.provider :aws do |aws, override|
        override.ssh.private_key_path = pemfile
        override.ssh.username         = "ec2-user"

        aws.user_data                 = aws_config["user_data"]
        aws.access_key_id             = aws_config["access_key_id"]
        aws.secret_access_key         = aws_config["secret_access_key"]
        aws.keypair_name              = aws_config["keypair_name"]
        aws.security_groups           = aws_config["security_groups"]
        aws.region                    = aws_config["region"]
        aws.ami                       = aws_config["ami"]
        aws.instance_ready_timeout    = 60
        aws.instance_type             = box[:instance_type]
  #      aws.subnet_id                 = aws_config["subnet_id"]
  #      aws.private_ip_address        = box[:ip]
        aws.tags                      = {
                                          'Name' => box[:name]
                                        }
      end
      
#      config.vm.provision :shell, :inline => "sudo hostname " % box[:name].to_s
#      config.vm.provision "ansible" do |ansible|
#        ansible.playbook              = "provisioning/playbook.yml"
#        ansible.inventory_path        = "provisioning/hosts"
#      end
    end
  end
end
