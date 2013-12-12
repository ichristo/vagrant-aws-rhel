require 'yaml'

# Load sensitive AWS credentials from external file
if File.exist?("config/aws.yml")
  aws_config  = YAML.load_file("config/aws.yml")["aws"]
  pemfile     = aws_config["pemfile"]
end

boxes = [
  { 
    :name           =>  'ose-broker-01',
    :fqdn           =>  'ose-broker-01.osshive.io',
    :private_ip     =>  '192.168.200.101',
    :primary        =>  'true',
    :instance_type  =>  't1.micro',
    :elastic_ip     =>  false
  },
  { 
    :name           =>  'ose-node-01',
    :fqdn           =>  'ose-node-01.osshive.io',
    :private_ip     =>  '192.168.200.121',
    :primary        =>  'false',
    :instance_type  =>  't1.micro',
    :elastic_ip     =>  false
  },
  { 
    :name           =>  'ose-node-02',
    :fqdn           =>  'ose-node-02.osshive.io',
    :private_ip     =>  '192.168.200.122',
    :primary        =>  'false',
    :instance_type  =>  't1.micro',
    :elastic_ip     =>  false
  },

]

VAGRANTFILE_API_VERSION = "2"

Vagrant.require_plugin "vagrant-aws"

Vagrant.configure(VAGRANTFILE_API_VERSION) do |config|
  boxes.each do |box|
    config.vm.define box[:name], primary: box[:primary] do |config|
      config.vm.box                 = "aws-rhel"
      config.vm.box_url             = "https://github.com/mitchellh/vagrant-aws/raw/master/dummy.box"

      config.vm.hostname            = box[:name]
      

      config.vm.boot_timeout        = 120
      config.vm.synced_folder '.', '/vagrant', :disabled => true
      config.vm.synced_folder 'config/ssh', '/vagrant/ssh-setup/'
      
      config.vm.provider :aws do |aws, override|
        override.ssh.private_key_path = pemfile
        override.ssh.username         = "ec2-user"
        override.ssh.forward_agent    = true

        aws.user_data                 = aws_config["user_data"]
        aws.access_key_id             = aws_config["access_key_id"]
        aws.secret_access_key         = aws_config["secret_access_key"]
        aws.keypair_name              = aws_config["keypair_name"]
        aws.security_groups           = aws_config["security_groups"]
        aws.region                    = aws_config["region"]
        aws.ami                       = aws_config["ami"]
        aws.instance_ready_timeout    = 120
        aws.instance_type             = box[:instance_type]
#        aws.subnet_id                 = aws_config["subnet_id"]
#        aws.private_ip_address        = box[:private_ip]
        aws.elastic_ip                = box[:elastic_ip]

        override.aws_extras.record_zone = "osshive.io."
        override.aws_extras.record_name = box[:fqdn] 
        override.aws_extras.record_type = "CNAME"
        override.aws_extras.record_ttl  = "60"

        aws.tags                      = {
                                          'Name' => box[:name]
                                        }
      end
      
      config.vm.provision :ansible do |ansible|
        ansible.playbook          = "ansible/vagrant.yml"
        #ansible.inventory_file    = "ansible/hosts"
        ansible.verbose           = false
      end
      
#      config.vm.provision :shell, :privileged => true, :inline => "echo 'domain osshive.io\nsearch osshive.io ec2.internal\nnameserver 205.251.197.143\nnameserver 172.16.0.23\n' > /etc/resolv.conf"
#      config.vm.provision :shell, :privileged => false, :inline => "sudo yum -y update"
#      config.vm.provision :shell, :privileged => false, :inline => "sudo yum -y install ruby unzip curl"
#      config.vm.provision "ansible" do |ansible|
#        ansible.playbook              = "provisioning/playbook.yml"
#        ansible.inventory_path        = "provisioning/hosts"
#      end
    end
  end
end
