[![Build Status](https://travis-ci.org/magicalyak/openshift-nginx.svg?branch=master)](https://travis-ci.org/magicalyak/openshift-nginx)

# openshift-nginx

Uses existing OpenShift install and configures NGINX to be the router.
Tested on OpenShift 3.11

Performs the following tasks:

- Installs NGINX or NGINX Plus openshift router

## Supported platforms

- Red Hat

## Prerequisites

- [Ansible 2.4+](https://docs.ansible.com)
- openshift installed
- copy nginx-repo.crt and nginx-repo.key to the files directory

## Known Issues

You must have a certificate that matches the docker-registry service URL.  The default installation usually uses a self-signed certificate tied to the IP address of the pod running docker-registry.  This usually won't work.  Running a proper PKI certificate chain will work very well.  For self-signed, install the router manually.

## Defaults

**nginx_plus:** no
> Enable NGINX Plus.  If you select yes (you rock!!!) then make sure you drop the nginx-repo.crt and nginx-repo.key in the `/files` directory

**openshift_user:** admin
> Username for the administrator account (it will create this account if it doesn't exist).

**openshift_password:** admin
> Password for the administrator account.

## Example Playbook

Below is a sample playbook that includes all of the default parameters. You'll find this exact example in the root of the project tree.

```yaml
---
- name: Install NGINX router for OpenShift
  hosts: localhost
  connection: local
  gather_facts: yes
  roles:
    - role: magicalyak.openshift_nginx
      nginx_plus: yes
      openshift_user: admin
      openshift_password: admin
```

After you install the role, copy *openshift-nginx.yml* to your project directory. For example:

```sh
# Install the role
$ ansible-galaxy install magicalyak.openshift_nginx

# Copy nginx-repo.crt and nginx-repo-key files
$ mkdir ${ANSIBLE_ROLES_PATH}/magicalyak.openshift_nginx/files
$ cp nginx-repo.{crt,key} ${ANSIBLE_ROLES_PATH}/magicalyak.openshift_nginx/files

# Copy the playbook from your roles path to the current working directory
$ cp ${ANSIBLE_ROLES_PATH}/magicalyak.openshift_nginx/openshift-nginx.yml .

# Create a localhost inventory file
$ echo "localhost">./inventory

# Run the playbook
$ ansible-playbook -i inventory --ask-become-pass openshift-nginx.yml
```

## Uninstall

To revert the changes make sure you don't delete the default-router-backup.yml file in the $HOME directory and run the following:

```sh
# Delete the NGINX Router
$ oc delete service/router dc/router clusterrolebinding/router-router-role serviceaccount/router

# Restore existing router
$ oc create -f $HOME/default-router-backup.yml
```

## Dependencies

OpenShift installed

## License

Apache v2

## Author

[@magicalyak](https://github.com/magicalyak)
