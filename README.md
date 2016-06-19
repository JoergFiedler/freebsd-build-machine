### FreeBSD Build Server

Creates a FreeBSD server which provides a ready to run `poudriere` installation. See [FreeBSD Handbook](https://www.freebsd.org/doc/handbook/ports-poudriere.html) for further information on how to use `poudriere`.

[This Ansible role](https://github.com/JoergFiedler/freebsd-build-server) is used to setup the server on EC2 or Virtualbox.

#### Quickstart

    export ACCESS_KEY='your key'
    export SECRET_ACCESS_KEY='your secret'
    export DEFAULT_REGION='the aws region to use'

    ansible-galaxy install JoergFiedler.freebsd-build-server
    vagrant plugin install vagrant-aws
    vagrant up --provider aws

Log in with `vagrant ssh`.

    build-ports
    upload-to-s3


#### HowTo

##### Set the S3 bucket name which serves as the new FreeBSD repository?

In `site.yml`.

    s3_bucket_name: 'YOUR_S3_BUCKET_NAME'

##### Change the private key which is used to sign the packages?

First you need to create a new key pair. 

    openssl genrsa -out files/poudriere.key 4096
    openssl rsa -in files/poudriere.key -pubout -out files/poudriere.key.pub
    
Add the newly create private key to your playbook (`site.yml`).

    poudriere_key_file: './files/poudriere.key'

##### Specify the list of packages to build?

Create a new file which contains a list of all packages you want to build.

    cat <<EOF >> files/port.list
    www/nginx
    net-p2p/btsync
    EOF

Configure the playbook (`site.yml`) to use your own packages list.

    poudriere_port_list_file: './files/port.list'

##### Set build options for specific packages?

Options for you build can be set easily by creating your own `make.conf` file.

    cat <<EOF >> files/make.conf
    OPTIONS_UNSET+= DOCS NLS X11 EXAMPLES
    EOF
    
Add the file to the playbook (`site.yml`).

    poudriere_build_options_file: './files/make.conf'

##### Build packages for more then one FreeBSD version?

Add more entries to the list of jails `poudriere_jails` in you playbook (`site.yml`).

    poudriere_jails:¬
      - { jail_name: 'freebsd-10_1_x64', version: '10.1-RELEASE' }
      - { jail_name: 'freebsd-10_2_x64', version: '10.2-RELEASE' }
      - { jail_name: 'freebsd-10_3_x64', version: '10.3-RELEASE' }

The configuration above would create packages for the three versions (10.1, 10.2, 10.3) of FreeBSD.

#### Notes

The virtualbox is configured to use 12GB of RAM. Please adapt the value to your requirements.
