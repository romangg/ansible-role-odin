# ODIN Ansible Role

An Ansible role based on the [Opinionated Debian Installer project](https://github.com/r0b0/debian-installer) to quickly install a full Debian system on a hard disk, usually from a live medium.

## Steps
1. Get a live Debian image [from debian.org](https://cdimage.debian.org/debian-cd/current-live/amd64/iso-hybrid/). The *standard* image is without GUI.
2. Run the live image and install `openssh-server`. For safety on an untrusted network change the password of the live user. [This script](https://gist.github.com/romangg/443ae2124ae53e10b8e8d363d801ff77) executed by the live user does both.
3. Run `lsblk` to get the target disk and wipe it with `sudo wipefs -a /dev/target-disk`.
4. From your control node run the role against the live Debian image.

## Variables
See [defaults/main.yml](defaults/main.yml) for all variables with default values. Required variables are:
- `odin_disk`: Target disk to install Debian on
- `odin_luks_password`: Password to decrypt the disk

## Example
Clone the repo and set the roles path respectively. Then an Ansible playbook to use the role with could be:
```yaml
- name: ODIN Playbook
  hosts: all
  become: true

  tasks:
    - ansible.builtin.include_role:
        name: ansible-role-odin
      vars:
        odin_disk: /dev/sda
        odin_luks_password: your-luks-password
        odin_root_password: your-root-password
        odin_hostname: "{{ inventory_hostname }}"
        odin_timezone: Europe/Berlin
        odin_swap_size: 16
```

Together with a `hosts` file:
```ini
[vps]
vps1 ansible_ssh_host=<your-host-ip> ansible_user=user ansible_password=live
```

Then run `ansible-playbook -i hosts -l vps1 playbook.yml`.
