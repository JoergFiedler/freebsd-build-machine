# FreeBSD Build Server

Creates a FreeBSD server which provides a ready to run `poudriere` installation. See [FreeBSD Handbook](https://www.freebsd.org/doc/handbook/ports-poudriere.html) for further information on how to use `poudriere`.

[This Ansible role](https://github.com/JoergFiedler/freebsd-build-server) is used to setup the server on AWS EC2 or VirtualBox.

## Quickstart

    export AWS_ACCESS_KEY='your key'
    export AWS_SECRET_ACCESS_KEY='your secret'
    export AWS_DEFAULT_REGION='the aws region to use'
    export S3_BUCKET_NAME='the S3 bucket name'
    export POUDRIERE_KEY_FILE='the key file to use for package signing'

    ansible-galaxy install JoergFiedler.freebsd-build-server,1.1.1
    vagrant plugin install vagrant-aws
    vagrant up --provider aws

Log in with `vagrant ssh`.

    sudo build-ports
    sudo upload-to-s3

## HowTo

### Change the private key which is used to sign the packages?

First you need to create a new key pair.

    openssl genrsa -out files/poudriere.key 4096
    openssl rsa -in files/poudriere.key -pubout -out files/poudriere.key.pub

Add the newly create private key to your playbook (`site.yml`).

    poudriere_key_file: './files/poudriere.key'

### Specify the list of packages to build?

Create a new file which contains a list of all packages you want to build.

    cat <<EOF >> files/port.list
    www/nginx
    net-p2p/btsync
    EOF

Configure the playbook (`site.yml`) to use your own packages list.

    poudriere_port_list_file: './files/port.list'

### Set build options for specific packages?

Options for you build can be set easily by creating your own `make.conf` file.

    cat <<EOF >> ./files/make.conf
    OPTIONS_UNSET+= DOCS NLS X11 EXAMPLES
    EOF

Add the file to the playbook (`site.yml`).

    poudriere_build_options_file: './files/make.conf'

### Build packages for more then one FreeBSD version?

Add more entries to the list of jails `poudriere_jails` in you playbook (`site.yml`).

    poudriere_jails:
      - { jail_name: 'freebsd-11_2_x64', version: '11.2-RELEASE' }

The configuration above instructs poudriere to create packages for the three versions (10.1, 10.2, 10.3) of FreeBSD.
