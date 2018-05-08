# genie-gitlab-groups  
## Description  
An Ansible Role to create a Gitlab Group, assign a user to the group with a specified level of access, and optionally assign the new Group with share access on an existing Gitlab Project.
## Prerequisites
To assign the new Gitlab Group access to an existing Gitlab Project your user account will require Owner access on that particular project. Refer to the gitlab-project Role for creation of a Gitlab Project.
## Variables  
|Variable Name|Default Value|Required|Description|
|:---:|:---:|:---:|:---:|
|`gl_url`|""|yes|URL to Gitlab Server|
|`gl_validate_certs`|"false"|no|Whether or not to validate the Gitlab server's SSL certificates.  Options are "true" or "false".|
|`gl_secrets`|True|no|Whether or not to load vaulted credentials from vars/gitlab-secrets.yml.  If set to `False`, you must provide the `gl_token` in another way (i.e. via playbook vars, group_vars, host_vars or a **vault included with `include_vars` from your main playbook.**)|
|`gl_user`|""|yes|API User to Access Gitlab with.|
|`gl_token`|""|yes|Gitlab Impersonation Token for making API Calls. Should be stored in an Ansible Vault located in vars/gitlab-secrets.yml.|
|`gl_create_grp`|""|yes|Name of Gitlab group to create.  Gitlab does not allow a Group name with spaces or special characters.|
|`gl_grp_desc`|""|no|Description of the Gitlab Group to create.|
|`gl_grp_path`|""|no|Path of the Gitlab Group to create. This will get set to the value of `gl_create_grp` if not provided.|
|`gl_grp_visibility`|"private"|no|Defines if the Gitlab Group's visibility is private, internal, or public.|
|`gl_prj_name`|""|no|The name of the Gitlab Project to map the new Gitlab Group share access to. This is optional.|
|`gl_grp_users`|""|no|List of Gitlab users to assign to the new Gitlab Group, and their associated permissions.  The access field must be one of the following integers: 10 - Guest, 20 - Reporter, 30 - Developer, 40 - Master, 50 - Owner.  The name field represents the user's name.  The access field represents the user's access.  The user must first exist in Gitlab.|
|`gl_prj_grp_access`|"30"|no|Permission to apply for the group on the specified `gl_prj_name` project.  The value must be one of the following integers: 10 - Guest, 20 - Reporter, 30 - Developer, 40 - Master, 50.  This only applies if `gl_prj_name` is specified.|  
### `gl_grp_users` Variable Usage:  
```yaml
---
gl_grp_users:
  - name: "myusername"
    access: "40"
  - name: "mydeveloperuser"
    access: "30"
```
***It is recommended to make use of `no_log: True` on an include of this role to prevent exposing your impersonation API token***
## Playbook Examples
### Standard Role Usage
```yaml
---
- hosts: all
  roles:
    - role: "genie-gitlab-groups"
      gl_url: "https://mygitlab.org"
      gl_validate_certs: "false"
      gl_secrets: True
      gl_user: "ansible"
      gl_token: "{{ my_vaulted_gitlab_token }}"
      gl_create_grp: "team1"
      gl_grp_desc: "Group for my team1 members."
      gl_prj_name: "team1"
      gl_grp_users:
        - name: "userA"
          access: "50" #Owner
        - name: "userB"
          access: "40" #Master
        - name: "userC" #Developer
          access: "30"
      no_log: True #Please do this to protect your API token
```
## Author  
[Andrew J. Huffman](mailto:ahuffman@redhat.com)
