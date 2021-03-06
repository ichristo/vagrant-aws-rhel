require 'yaml'

# Load sensitive AWS credentials from external file
if File.exist?("config/aws.yml")
  aws_config  = YAML.load_file("config/aws.yml")["aws"]
  pemfile     = aws_config["pemfile"]
end

boxes = [
  { 
    :name           =>  'ose-broker-01',
    :role           =>  'broker',
    :fqdn           =>  'ose-broker-01.osshive.io',
    :private_ip     =>  '192.168.200.101',
    :primary        =>  'true',
    :instance_type  =>  't1.micro',
    :elastic_ip     =>  false
  },
  { 
    :name           =>  'ose-node-01',
    :role           =>  'node',
    :fqdn           =>  'ose-node-01.osshive.io',
    :private_ip     =>  '192.168.200.121',
    :primary        =>  'false',
    :instance_type  =>  'm1.small',
    :elastic_ip     =>  false
  },
  { 
    :name           =>  'ose-node-02',
    :role           =>  'node',
    :fqdn           =>  'ose-node-02.osshive.io',
    :private_ip     =>  '192.168.200.122',
    :primary        =>  'false',
    :instance_type  =>  'm1.small',
    :elastic_ip     =>  false
  },

]

VAGRANTFILE_API_VERSION = "2"
BOX_TIMEOUT             = 180

Vagrant.require_plugin "vagrant-aws"

Vagrant.configure(VAGRANTFILE_API_VERSION) do |config|
  boxes.each do |box|
    config.vm.define box[:name], primary: box[:primary] do |config|
      config.vm.box                 = "aws-rhel"
      config.vm.box_url             = "https://github.com/mitchellh/vagrant-aws/raw/master/dummy.box"

      config.vm.hostname            = box[:name]
      

      config.vm.boot_timeout        = BOX_TIMEOUT
      config.vm.synced_folder '.', '/vagrant', :disabled => true
      config.vm.synced_folder 'config/ssh', '/vagrant/ssh-setup/'
      config.vm.synced_folder 'config/etc', '/vagrant/etc-setup/'

      config.aws_extras.record_zone = "osshive.io."
      config.aws_extras.record_name = box[:fqdn] 
      config.aws_extras.record_type = "CNAME"
      config.aws_extras.record_ttl  = "120"
      
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
        aws.instance_ready_timeout    = BOX_TIMEOUT
        aws.instance_type             = box[:instance_type]
#        aws.subnet_id                 = aws_config["subnet_id"]
#        aws.private_ip_address        = box[:private_ip]
        aws.elastic_ip                = box[:elastic_ip]


        aws.tags = { 'Name' => box[:name] }
      end
      
      config.vm.provision :ansible do |ansible|
        ansible.sudo              = true
        ansible.playbook          = "provisioning/ansible/playbook.yml"
        ansible.verbose           = false
      end
      
      config.vm.provision :shell, :privileged => false, :inline => "sudo cp -f /vagrant/etc-setup/etc-resolv-conf /etc/resolv.conf"
      config.vm.provision :shell, :privileged => false, :inline => "cp /vagrant/ssh-setup/config /home/ec2-user/.ssh/"
      config.vm.provision :shell, :privileged => false, :inline => "cp /vagrant/ssh-setup/RHEL-OSE-DEMO.pem /home/ec2-user/.ssh/"
      config.vm.provision :shell, :privileged => false, :inline => "sudo yum -y install screen"
      config.vm.provision :shell, :privileged => false, :inline => "sudo yum -y update"
      
      case box[:role]
      when "broker"
        config.vm.synced_folder 'config/openshift', '/vagrant/ose-setup/'
        config.vm.provision :shell, :privileged => false, :inline => "sudo yum -y install ruby unzip curl"
#        config.vm.provision :shell, :privileged => false, :inline => "sh <(curl -s https://install.openshift.com/ose-2.0) -c /vagrant/ose-setup/oo-install-cfg.yml -w enterprise_deploy"
      when "node"
        #Do Something        
      end

    end
  end
end
