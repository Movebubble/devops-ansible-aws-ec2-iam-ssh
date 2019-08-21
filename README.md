## Manage AWS EC2 SSH access with IAM

* Source: https://github.com/widdix/aws-ec2-ssh
* Inspired article: https://cloudonaut.io/manage-aws-ec2-ssh-access-with-iam/


1. Create AWS IAM Group SSHusers.
1. Create AWS IAM Group SSHAdmins.
1. Add SSH public key to your IAM User
1. Add IAM User to groups SSHusers (to allow ssh to an instances) and SSHAdmins (to allow sudo command)

## Example playbook

```
# vim:ft=ansible:
---

- name: Manage AWS EC2 SSH access with IAM
  hosts: localhost
  tasks:
    - name: Get EC2 facts
      action: ec2_metadata_facts

    - name: Retrieve all tags on an instance
      ec2_tag:
        region: '{{ ansible_ec2_placement_region }}'
        resource: '{{ ansible_ec2_instance_id }}'
        state: list
      register: ec2_tags

    #  When EC2 tag IAMSSH is defined and equal true
    - name: Allow ssh access iam users
      import_role:
        name: aws-ec2-iam-ssh
      when: ec2_tags.tags.IAMSSH is defined and ( ec2_tags.tags.IAMSSH == 'true' or ec2_tags.tags.IAMSSH == 'True' )
```
