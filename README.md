## Digital Ocean Droplet Creation Automation

Create your Droplet from Digital Ocean API

## Prerequisite

- [Vagrant](https://www.vagrantup.com/) v2.2.7
- [Virtualbox](https://www.virtualbox.org/) v6.1.4
- [Ansible](https://www.ansible.com/) v2.9.6
- [Vagrant DigitalOcean](https://github.com/devopsgroup-io/vagrant-digitalocean) v0.9.4
- [Direnv](https://github.com/direnv/direnv) v2.21.2

> ensure you have the specified version or above for all the above mentioned tools

## Installation

### Mac OS

1. Vagrant
```sh
$ brew cask install vagrant
```

2. Virtualbox
```sh
$ brew cask install virtualbox
```

3. Ansible
```sh
$ brew install ansible
```

4. Vagrant DigitalOcean
```sh
$ vagrant plugin install vagrant-digitalocean
```

5. Direnv

```sh
$ brew install direnv
```

Make sure to hook direnv into your shell.

If you are using `bash` shell, then add the following line at the end of the ~/.bashrc file:

```bash
eval "$(direnv hook bash)"
```

If you are using `zsh` shell, then add the following line at the end of the ~/.zshrc file:

```bash
eval "$(direnv hook zsh)"
```

for other shell, refer [here](https://github.com/direnv/direnv/blob/master/docs/hook.md)

> Note, if you are have spwaned multiple tabs / windows / split of your terminal, whenever you make changes to .envrc
> ensure to press just enter in all the instances to loaded the updated environment variables into the instance of the
> terminal window
>
> failing to do will result in undesired output

### DigitalOcean

As a first step, setup necessary env variables.

In DigitalOcean,

1. Follow the instruction [here](https://www.digitalocean.com/docs/api/create-personal-access-token/) to setup personal token
2. Follow the instruction [here](https://www.digitalocean.com/docs/droplets/how-to/add-ssh-keys/to-account/) to upload your ssh key to your digital ocean account
3. `$ cp .envrc.sample .envrc`
4. Modify the following configurations as per your credentials

```yaml
DIGITAL_OCEAN_TOKEN: the token your have created in step 1
DIGITAL_OCEAN_SSH_KEY_NAME: the ssh key you have created in step 2
DIGITAL_OCEAN_PRIVATE_KEY: private_key_file_name (assumption is the file will be located under $HOME/.ssh/private_key_file_name) for which you have uploaded the public key in step 2
```
5. Load the env variables

```bash
## This command has to be run for every change in .envrc
$ direnv allow .
```

Additionally, if you are using ZSH shell instead of bash

```bash
## Only for ZSH
$ eval "$(direnv hook zsh)"
```

6. To create the droplet on Digital Ocean,

```bash
$ make provision.basic
```

7. To teardown the cluster, execute
```bash
$ make destroy
```

9. By default, VMs are created with 4vCPUs and 8GB of RAM. To change this use the configuration parameter `INSTANCE_TYPE`.

```yaml
## default instance type
INSTANCE_TYPE: s-1vcpu-1gb

## For more info, visit https://developers.digitalocean.com/documentation/changelog/api-v2/new-size-slugs-for-droplet-plan-changes/
```


## Troubleshooting

**Issue with Digital ocean authentication**

If you face the following issue when issuing `make`, `vagrant` commands ,

```sh
There was an issue with the JSON response from the DigitalOcean
API at:

Path: /v2/droplets
URI Params: {}

The response JSON from the API was:

Response: Unable to authenticate you
```

simple source the .envrc file once. (`$ source .envrc`), also ensure your .envrc configurations are correct or use [direnv](https://github.com/direnv/direnv) as mentioned in the Prerequisite

**Issue with bcrypt_pbkdf**

If you face this issue, probably you are using ssh with passphrase ensure you have configured ssh-agent, for more info refer [here](https://www.ssh.com/ssh/agent)
