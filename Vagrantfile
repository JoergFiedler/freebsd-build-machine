# -*- mode: ruby -*-
# vi: set ft=ruby :

Vagrant.configure(2) do |config|
  config.vm.box = 'JoergFiedler/freebsd-box'
  config.vm.box_check_update = false

  config.vm.synced_folder '.', '/vagrant', disabled: true

  config.vm.provision 'ansible' do |ansible|
    ansible.playbook = 'site.yml'
    ansible.tags = ENV['ANSIBLE_TAGS']
    ansible.verbose = ENV['ANSIBLE_VERBOSE']
  end

  config.vm.provider 'virtualbox' do |vb|
    vb.memory = '12288'
    vb.cpus = 2
    vb.name = 'FreeBSD Build Server'
    vb.customize ['modifyvm', :id, '--hwvirtex', 'on']
    vb.customize ['modifyvm', :id, '--audio', 'none']
  end

  config.vm.provider :aws do |aws, override|
    override.ssh.username = 'ec2-user'

    aws.access_key_id = ENV['ACCESS_KEY']
    aws.secret_access_key = ENV['SECRET_ACCESS_KEY']
    aws.region = ENV['DEFAULT_REGION']
    aws.keypair_name = 'ec2-user'
    aws.instance_type = 'm3.xlarge'
    aws.user_data = "#!/bin/sh\necho 'pass all keep state' >> /etc/pf.conf\necho pf_enable=YES >> /etc/rc.conf\necho pflog_enable=YES >> /etc/rc.conf\necho 'firstboot_pkgs_list=\"sudo bash python27\"' >> /etc/rc.conf\necho 'ifconfig_xn0=\"SYNCDHCP -tso\"' >> /etc/rc.conf\nmkdir -p /usr/local/etc/sudoers.d\necho 'ec2-user ALL=(ALL) NOPASSWD: ALL' >> /usr/local/etc/sudoers.d/ec2-user"
    aws.block_device_mapping = [
      { 'DeviceName' => '/dev/sda1',
        'Ebs.VolumeSize' => 20,
        "Ebs.VolumeType" => "gp2",
        "Ebs.DeleteOnTermination" => true }
    ]
    aws.terminate_on_shutdown = true
  end
end
