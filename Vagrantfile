VAGRANTFILE_API_VERSION = '2'

Vagrant.configure(VAGRANTFILE_API_VERSION) do |config|
  # Every Vagrant virtual environment requires a box to build off of.
  config.vm.box = 'JoergFiedler/freebsd-12'
  config.vm.synced_folder '.', '/vagrant', disabled: true
  config.ssh.insert_key = false
  config.ssh.shell ='/bin/sh'

  config.vm.define 'build-server' do |host|
    host.vm.provision 'ansible', type: 'ansible' do |ansible|
      ansible.playbook = './site.yml'
    end
  end

  config.vm.provision 'ansible', type: 'ansible' do |ansible|
    ansible.galaxy_roles_path = ENV['ANSIBLE_ROLES_PATH'] || '../'
    ansible.tags = ENV['ANSIBLE_TAGS']
    ansible.skip_tags = ENV['ANSIBLE_SKIP_TAGS']
    ansible.verbose = ENV['ANSIBLE_VERBOSE']
  end

  config.vm.provider 'virtualbox' do |vb, global|
    global.vm.network 'private_network', type: 'dhcp', auto_config: false

    vb.gui = false
    vb.memory = 4096
    vb.cpus = 2
    vb.customize ['modifyvm', :id, '--hwvirtex', 'on']
    vb.customize ['modifyvm', :id, '--audio', 'none']
  end

  config.vm.provider 'aws' do |aws, global|
    global.ssh.username = 'ec2-user'

    global.vm.provision 'ansible', type: 'ansible' do |ansible|
      ansible.extra_vars = './.ec2.yml'
    end

    aws.access_key_id = ENV['AWS_ACCESS_KEY_ID']
    aws.associate_public_ip = true
    aws.instance_type = 't3.xlarge'
    aws.block_device_mapping = [
        {
            'DeviceName' => '/dev/sda1',
            'Ebs.VolumeSize' => 10,
            'Ebs.VolumeType' => 'gp2',
            'Ebs.DeleteOnTermination' => true
        },
        {
            'DeviceName' => '/dev/sdf',
            'Ebs.VolumeSize' => 50,
            'Ebs.VolumeType' => 'gp2',
            'Ebs.DeleteOnTermination' => true
        }
    ]
    aws.keypair_name = 'ec2-user'
    aws.region = 'eu-west-1'
    aws.secret_access_key = ENV['AWS_SECRET_ACCESS_KEY']
    aws.security_groups = ['sg-088d1213681b2d080']
    aws.ssh_host_attribute = :dns_name
    aws.subnet_id = 'subnet-cf3beaaa'
    aws.terminate_on_shutdown = true
    aws.user_data = "#!/bin/sh
echo 'pass all keep state' >> /etc/pf.conf
echo pf_enable=YES >> /etc/rc.conf
echo pflog_enable=YES >> /etc/rc.conf
echo 'firstboot_pkgs_list=\"awscli sudo\"' >> /etc/rc.conf
mkdir -p /usr/local/etc/sudoers.d
/usr/sbin/service pf start
echo 'ec2-user ALL=(ALL) NOPASSWD: ALL' >> /usr/local/etc/sudoers.d/ec2-user"
  end
end
