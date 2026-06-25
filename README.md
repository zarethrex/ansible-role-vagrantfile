# Ansible Role - Vagrantfile

Generates an infrastructure definition file (Vagrantfile) for Hashicorp Vagrant through templating and provision of values.

## Requirements

- [Vagrant](https://developer.hashicorp.com/vagrant) `>= 2.1`

## Role Variables

- `vagrantfile_location`: Directory in which to place the Vagrantfile.
- `vagrantfile_virtual_machines`: Define the specifications for each virtual machine to add, argument is a list of dictionaries with arguments:
    - `box`: The box to use for this virtual machine.
    - `label`: Unique label for this machine in the Vagrantfile.
    - `provider`: Which provider to use, options are:
        - `virtualbox`:
            - `cpus`: The number of CPUs for the virtual machine, default is `2`.
            - `memory`: The memory allocation for the virtual machine, default is `2048`.
            - `name`: The name defined within the provider of the virtual machine, optional.
        - `libvirt`:
            - `cpus`: The number of CPUs for the virtual machine, default is `2`.
            - `memory`: The memory allocation for the virtual machine, default is `2048`.
            - `video_type`: The video device to use, optional.
    - `network`: VM network configurations:
        - `port_forwarding`: List of mappings for ports to forward to host, in the form `host: <int>` and `port: <int>`
        - `private_network`: Use a private network:
            - `type`: Type for the private network.
    - `provision`: Run provisioning on the virtual machine, dictionary containing provisioner options:
        - `shell`: Optional string defining shell script to execute on creation.
        - `ansible`: Optional dictionary defining Ansible as a provisioner, with options:
            - `groups`: List of groups to add the virtual machine to in the Ansible inventory.
            - `playbook`: Path to the Ansible playbook to use.
        - `file`: List of files to add to VM when provisioning, elements in form:
            - `src`: File on host
              `dest`: File on guest
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
    vagrantfile_sync_directories:
      - src: "{{ molecule_ephemeral_directory }}"
        dest: "/home/vagrant/molecule_ephem"
    vagrantfile_virtual_machines:
      - label: test_1
        box: rockylinux/9
        provider:
          virtualbox:
            cpus: 1
            memory: 1024
            name: Test-VM-1
        provision:
          ansible:
            groups:
              - test
            playbook: playbook.yml
        network:
          forwarded_ports:
            - host: 8080
              guest: 80
            - host: 4443
              guest: 443
      - label: test_2
        box: rockylinux/9
        provider:
          libvirt:
            cpus: 2
            memory: 2048
            video_type: virtio
        network:
          private_network:
            type: dhcp
        provision:
          shell: |
            echo "Setting up machine..."
            dnf install -y httpd
            systemctl enable --now httpd
          ansible:
            groups:
              - production
            playbook: playbook.yml
      - label: test_3
        box: rockylinux/9
        provider:
          virtualbox:
            name: Test-VM-3
        provision:
          files:
            - src: "{{ molecule_ephemeral_directory }}/dummy.txt"
              dest: "/home/vagrant/dummy.txt"
  roles:
    - zarethrex.vagrantfile
```

## License

MIT

## Author Information

This role was created by [Zarethrex](https://github.com/zarethrex) on GitHub.
