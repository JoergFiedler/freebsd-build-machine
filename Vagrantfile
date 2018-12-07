Vagrant.configure(2) do |config|
  config.vm.box = 'JoergFiedler/freebsd-11.2'
  config.vm.box_check_update = false

  config.vm.synced_folder '.', '/vagrant', disabled: true

  config.vm.provision 'ansible' do |ansible|
    ansible.playbook = 'site.yml'
    ansible.tags = ENV['ANSIBLE_TAGS']
    ansible.verbose = ENV['ANSIBLE_VERBOSE']
  end

  config.vm.provider 'virtualbox' do |vb|
    vb.memory = '4096'
    vb.cpus = 2
    vb.name = 'FreeBSD Build Server'
    vb.customize ['modifyvm', :id, '--hwvirtex', 'on']
    vb.customize ['modifyvm', :id, '--audio', 'none']
  end

  config.vm.provider :aws do |aws, override|
    override.ssh.username = 'ec2-user'

    aws.access_key_id = ENV['AWS_ACCESS_KEY_ID']
    aws.secret_access_key = ENV['AWS_SECRET_ACCESS_KEY']
    aws.region = ENV['AWS_DEFAULT_REGION']
    aws.keypair_name = 'ec2-user'
    aws.subnet_id = 'subnet-107d8167'
    aws.associate_public_ip = true
    aws.ssh_host_attribute = ':public_ip_address'
    aws.instance_type = 'c5.xlarge'
    aws.user_data = "#!/bin/sh
echo 'pass all keep state' >> /etc/pf.conf
echo pf_enable=YES >> /etc/rc.conf
echo pflog_enable=YES >> /etc/rc.conf
echo 'firstboot_pkgs_list=\"sudo bash python27\"' >> /etc/rc.conf
mkdir -p /usr/local/etc/sudoers.d
echo 'ec2-user ALL=(ALL) NOPASSWD: ALL' >> /usr/local/etc/sudoers.d/ec2-user"
    aws.block_device_mapping = [
      { 'DeviceName' => '/dev/sda1',
        'Ebs.VolumeSize' => 20,
        "Ebs.VolumeType" => "gp2",
        "Ebs.DeleteOnTermination" => true }
    ]
    aws.terminate_on_shutdown = true
  end
end
