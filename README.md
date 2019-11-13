# Check Point Ansible Mgmt Collection
This Ansible collection provides control over a Check Point management server using
Check Point's web-services APIs.

The Ansible Check Point modules reference can be found here:
https://docs.ansible.com/ansible/latest/modules/list_of_network_modules.html#check-point.
Note - look only at the `cp_mgmt_*` modules, cause the `checkpoint_*` will be depricated.

Installation instructions
-------------------------
Run `ansible-galaxy collection install check_point.mgmt`

Requirements
------------
* Ansible 2.9+ is required.
* The Check Point server should be using the versions detailed in this SK: https://supportcenter.checkpoint.com/supportcenter/portal?eventSubmit_doGoviewsolutiondetails=&solutionid=sk114661
* The Check Point server should be open for API communication from the ansible server.
  Open SmartConsole ans check "Manage & Settings > Blades > Management API > Advanced settings".

Usage
-----
1. Edit the `hosts` so that it would contain a section similar to this one:
```
[check_point]
%YOUR_IP%
[checkpoint:vars]
ansible_httpapi_use_ssl=True
ansible_httpapi_validate_certs=False
ansible_user=%YOUR_USER%
ansible_password=%YOUR_PASSWORD%
ansible_network_os=check_point.mgmt.checkpoint
```
Note - If you want to run against Ansible version 2.9 instead of the collection, just replace `ansible_network_os=check_point.mgmt.checkpoint` with `ansible_network_os=checkpoint`.
2. Run a playbook:
```sh
ansible-playbook your_ansible_playbook.yml
```
or

Run a playbook in "check mode":
```sh
ansible-playbook -C your_ansible_playbook.yml
```
Example playbook:
```
---
- name: playbook name
  hosts: checkpoint
  connection: httpapi
  tasks:
    - name: task to have network
      check_point.mgmt.cp_mgmt_network:
        name: "network name"
        subnet: "4.1.76.0"
        mask_length: 24
        auto_publish_session: true
        
      vars: 
        ansible_checkpoint_domain: "SMC User"
```
Note - If you want to run against Ansible version 2.9 instead of the collection, just replace `check_point.mgmt.cp_mgmt_network` with `cp_mgmt_network`

###  Notes:
  1. Because this Ansible module is controlling the management server remotely via the web API, 
     the ansible server needs to have access to the Check Point API server.
     Open `SmartConsole`, navigate to "Manage & Settings > Blades > Management API > Advanced settings"
     and check the API server's accessibility set
  2. Ansible has a feature called "Check Mode" that enables you to test the
     changes without actually changing anything.
  3. The login and logout happens automatically.
  4. If you want to login to specific domain, in the playbook above in the `vars`secion insert the domain name to 
     `ansible_checkpoint_domain`
  5. There are two ways to publish changes:
    a. Set the `auto_publish_session` to `true` as displayed in the example playbook above.
       This option will publish only the task which this parameter belongs to.
    b. Add the task to publish with the `cp_mgmt_publish` module.
       This option will publish all the tasks above this task.
  6. It is recommended by Check Point to use this collection over the modules of Ansible version 2.9
  7. If you still want to use Ansible version 2.9 instead of this collection (not recommended):
    a. In the `hosts` file replace `ansible_network_os=check_point.mgmt.checkpoint` with `ansible_network_os=checkpoint`
    b. In the task in the playbook replace the module `check_point.mgmt.cp_mgmt_*` with the module `cp_mgmt_*`
