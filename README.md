# Ansible Role - Vagrantfile

Generates an infrastructure definition file (Vagrantfile) for Hashicorp Vagrant through templating and provision of values.

## Requirements

- [Vagrant](https://developer.hashicorp.com/vagrant) `>= 2.1`

## Role Variables

- `vagrantfile_location`: Directory in which to place the Vagrantfile.
- `vagrantfile_virtual_machines`: Define the specifications for each virtual machine to add, argument is a list of dictionaries with arguments:
  - `box`: The box to use for this virtual machine.
  - `label`: Unique label for this machine in the Vagrantfile.
  - `memory`: The memory allocation for the virtual machine, default is `2048`.
  - `cpus`: Tue number of CPUs for the virtual machine, default is `2`.
  - `name`: The name defined within the provider of the virtual machine.
  - `provider`: Which provider to use, options are `virtualbox`.
  - `ansible`: Optional dictionary defining Ansible as a provisioner, with options:
    - `groups`: List of groups to add the virtual machine to in the Ansible inventory.
    - `playbook`: Path to the Ansible playbook to use.
- `vagrantfile_minimum_vagrant_version`: Specify a minimum working Vagrant version, default is unspecified.
- `vagrantfile_state`: State of Vagrantfile on system, either `present` or `absent`, default `present`.
- `vagrantfile_sync_directories`: List of directories to synchronize between host and target (see examples below), default `[]`.

## Dependencies

None.

## Example Playbook

```yaml
- name: Trial Vagrantfile Role
  hosts: localhost
  connection: local
  gather_facts: false
  vars:
    vagrantfile_location: "{{ molecule_ephemeral_directory }}"
    vagrantfile_virtual_machines:
      - label: test_1
        name: Test-VM-1
        box: rockylinux/9
        provider: virtualbox
        cpus: 1
        memory: 1024
        ansible:
          groups:
            - test
          playbook: playbook.yml
      - label: test_2
        name: Test-VM-2
        box: rockylinux/9
        provider: virtualbox
        cpus: 2
        memory: 2048
        ansible:
          groups:
            - production
          playbook: playbook.yml
  roles:
    - zarethrex.vagrantfile

```

## License

MIT

## Author Information

This role was created by [Zarethrex](https://github.com/zarethrex) on GitHub.
