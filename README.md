Ansible Role Name - cybergavin.nutanix_vm_create
================================================

This ansible role enables the creation of one or more VMs on a single Nutanix cluster.

Requirements
------------

- A user-provided comma-separated-values file containing the specifications of one or more VMs. Refer the sample files/vm.csv in the role dircetory.
- A user-provided variables file containing appropriate values for required variables. Refer the defaults/main.yml in the role directory.

Role Variables
--------------

- pc_cluster_fqdn   # FQDN/IP for Prism Central
- pe_cluster_fqdn   # FQDN/IP for Prism Element
- prism_user        # An account with permissions to provision on the cluster
- prism_password    # The account's password. Do NOT store this in the clear. Use ansible vault or an external vault. 
- cluster_name      # Name of the nutanix cluster on which VMs will be created 
- vm_data_csv       # Relative path and name of the comma-separated-values file containing details of the VMs to be deployed
- global_debug      # Global Debug flag


Dependencies
------------

Although this role uses the v1 API on Prism Element to mount and enable Nutanix Guest Tools on the created VM(s), the NGT status
will be displayed as "Latest" only after the VM(s) reboot or the "Nutanix Guest Agent" service on the VM(s) is restarted.


Example Playbook
----------------

Create a **vars/main.yml** with the required variables (above) and a **files/vm.csv** before using the following playbook.

    ---
    - name: Create VMs on Nutanix AHV
      hosts: localhost
      gather_facts: false
      tasks:
        - name: Include variables
          include_vars: dir=vars
        - name: Use ansible role cybergavin.nutanix_vm_create
          include_role:
            name: cybergavin.nutanix_vm_create


License
-------

GPLv3.0


Author Information
------------------

Cybergavin - https://cybergav.in - https://github.com/cybergav.in


References
-----------

- Influenced/Inspired by the Ansible role mbach04.nutanix_vm_provisioner at https://github.com/mbach04/nutanix_vm_provisioner.
- The ansible role mbach04.nutanix_vm_provisioner is licensed using the MIT License with Copyright (c) 2018 Red Hat.