### FreeBSD Build Server

Creates a FreeBSD server which provides a ready to run `poudriere` installation. See [FreeBSD Handbook](https://www.freebsd.org/doc/handbook/ports-poudriere.html) for further information on how to use `poudriere`.

[This Ansible role](https://github.com/JoergFiedler/freebsd-build-server) is used to setup the server on EC2 or Virtualbox.

#### Howto

    export AWS_ACCESS_KEY_ID='your key'
    export AWS_SECRET_ACCESS_KEY='your secret'
    export AWS_DEFAULT_REGION='the aws region to use'

    ansible-galaxy install JoergFiedler.freebsd-build-server
    vagrant plugin install vagrant-aws
    vagrant up --provider aws


Log in with `vagrant ssh`.

    build-port
    upload-to-s3

