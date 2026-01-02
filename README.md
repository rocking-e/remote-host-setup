# remote-host-setup

This repository contains Ansible code to facilitate configuring remote Linux hosts. I created it
initially to configure a server in karmalab for use as a Teleport operations host. But it can also
be used to setup any host. It installs some basic tooling to support ansible. The main playbook is
[setup-host.yaml](./setup-host.yaml) playbook. I then added the playbooks
[get-updates.yaml](./get-updates.yaml) and [do-updates.yaml](./do-updates.yaml) to see if OS package
updates are available and to apply the updates.

I will eventually add playbooks to reboot the machines in inventory if they need a reboot because of
a kernel update. The reboots would be done serially. Although since this is for personal use, they
could be done in parallel.

## Playbook setup-host.yaml

The [setup-host.yaml](./setup-host.yaml) playbook adds the tools necessary for working with the
Teleport repositories. The work is done in the roles in the sub-directories. The main tools
installed include packer, ansible, docker, AWS cli (with ssm plugin), saml2aws (for getting AWS
credentials) and Terraform and Terragrunt.

### required-packages

The [required-packages](./required-packages/tasks/main.yaml) adds the OS packages. It includes the
proper tasks based on the `ansible_os_family` attribute/variable. The packages to add are given in
the the [variables file](./required-packages/vars/main.yaml). Because some packages names on
Ubuntu/Debian systems differ from equivalent packages on RHEL systems, and different packages may be
required on Ubuntu/Debian or RHEL systems there are three lists. One list is the packages that are
common to both operating system families. And then there is a list for each family. The lists are
combined in the call to the package manager.

### packer

The [packer](./packer/tasks/main.yaml) installs [HashiCorp `packer`](https://www.packer.io) program.
`packer` is used to build the images. It usually isn't needed because it's invoked in a container.
But it is available for local debugging and testing.

### docker

The [docker](./docker/tasks/main.yaml) role does what the name implies - it installs the Docker
Community Edition of `docker`.

### aws-cli-ssm

The [aws-cli-ssm](./aws-cli-ssm/tasks/main.yaml) role installs the AWS command line tool `aws`. And it also
installs the AWS session manager plugin to enable using it as an SSH proxy. This allows the ansible
code to reach the EC2 instances in all the accounts.

### saml2aws

The [saml2aws](./saml2aws/tasks/main.yaml) role installs the `saml2aws` program.

### terraform-terragrunt

The [terraform-terragrunt](./terraform-terragrunt/tasks/main.yaml) role installs the HashiCorp
Terraform command and the [terragrunt](https://terragrunt.gruntwork.io) command. The installed
version of `terraform` is the last open source version.

## Playbook get-updates.yaml

This playbook will return information from each host in the inventory. The more important
information is the list of packages that can be upgraded. It also returns the FQDN of the host and
the output of the `uptime` command.

The list of packages is really the output of either `dnf list updates` or `apt list --upgradeable`
and those commands usually have extra output.

## Playbook do-updates.yaml

This playbook will do a full package update on the systems.

## Credential helper testing

OK, another line.

and another

