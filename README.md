Ansible role: Stamp
===================

This role stamps a machine with information about the
most recent Ansible playbook run against a host.
This should be applied at the end of a playbook so that
the stamp is only updated on a successful playbook run.

Role Variables
--------------

```
ENTRY POINT: main - Stamp filesystem after a successful Ansible playbook run

        This role stamps a machine with information about the most
        recent Ansible playbook run against a host. This should be
        applied at the end of a playbook so that the stamp is only
        updated on a successful playbook run.

OPTIONS (= is mandatory):

- stamp_file_contents
        Contents for the stamp, or empty string to not set the stamp
        file
        default: Current ISO 8601 timestamp
        type: str

- stamp_file_path
        Path for stamp file
        default: /.ansible_stamp
        type: str

- stamp_file_template
        Template file for the stamp
        default: ansible_stamp
        type: str

- stamp_motd_contents
        Contents for the motd, or empty string to not set the motd
        default: ''
        type: str

- stamp_motd_path
        Path for motd file
        default: /etc/motd
        type: str

- stamp_motd_template
        Template file for the motd
        default: motd
        type: str
```

Installation
------------

This role can either be installed manually with the ansible-galaxy CLI tool:

    ansible-galaxy install git+https://github.com/wandansible/stamp,main,wandansible.stamp
     
Or, by adding the following to `requirements.yml`:

    - name: wandansible.stamp
      src: https://github.com/wandansible/stamp

Roles listed in `requirements.yml` can be installed with the following ansible-galaxy command:

    ansible-galaxy install -r requirements.yml

Example Playbook
----------------

    - hosts: all
      roles:
        - role: wandansible.stamp
          become: true
          vars:
            ansible_git_repo_hash: >-
              {{ lookup('ansible.builtin.pipe', 'git describe --always --dirty=-dirty') }}
            ansible_running_user: "{{ lookup('ansible.builtin.env', 'USER') }}"
            stamp_file_contents: |
              {{ ansible_date_time.iso8601 }} {{ ansible_git_repo_hash }} {{ ansible_running_user }}
            stamp_motd_contents: |2+

                Host: {{ ansible_fqdn }}
                  OS: {{ ansible_lsb.description }}

                This system is managed by ansible, and was last built:
                    at {{ ansible_date_time.iso8601 }} from version {{ ansible_git_repo_hash }}
