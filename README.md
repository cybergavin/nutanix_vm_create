Ansible Role Name - cybergavin.nutanix_vm_create
================================================

This ansible role enables the creation of one or more VMs on a single Nutanix cluster.

Features
---------

- Allows provisioning of VMs from different disk images
- Allows provisioning of VMs in different subnets
- Mounts and enables Nutanix Guest Tools on the VMs after provisioning. 
  *NOTE:* The Nutanix Guest Agent must be present in the disk image and the Nutanix Guest Agent service on the VM must be restarted after provisioning.
- Creates VMs with **UEFI** boot config

Requirements
------------

- A user-provided comma-separated-values file containing the specifications of one or more VMs. Refer the sample files/vm.csv in the role dircetory.
- A user-provided variables file containing appropriate values for required variables.

Role Variables
--------------

- pc_cluster_fqdn   # FQDN/IP for Prism Central
- pe_cluster_fqdn   # FQDN/IP for Prism Element
- cluster_name      # Name of the nutanix cluster on which VMs will be created 
- vm_data_csv       # Relative path and name of the comma-separated-values file containing details of the VMs to be deployed
- global_debug      # Global Debug flag


Dependencies
------------

Although this role uses the v1 API on Prism Element to mount and enable Nutanix Guest Tools on the created VM(s), the NGT status
will be displayed as "Latest" only after the VM(s) reboot or the "Nutanix Guest Agent" service on the VM(s) is restarted.


Example usage after installation of the ansible role
-----------------------------------------------------

*STEP 1:* Create a **vars/main.yml** file as per the following example:

    pc_cluster_fqdn: "prismcentral.contoso.com"   # FQDN/IP for Prism Central
    pe_cluster_fqdn: "prismelement.contoso.com"   # FQDN/IP for Prism Element
    cluster_name: "mynutanix"                     # Name of the nutanix cluster on which VMs will be created
    vm_data_csv: "files/vm.csv"                   # Relative path and name of the tilde-separated-values file containing details of the VMs to be deployed
    global_debug: false                           # Global debug flag


*STEP 2:* Create a **files/vm.csv** file as per the following example:

    vm_name,ip,subnet_name,image_name,num_vcpu,memory_mib,disk_list_mib
    testvm,10.1.1.10,webnet,rhel8-image,2,4096,10240-20480


*STEP 3:* Create a playbook **main.yml** file as per the following example:   

    ---
    - name: Create VMs on Nutanix AHV
      hosts: localhost
      connection: local
      gather_facts: false
      vars_prompt:
        - name: prism_user
          prompt: Enter the username for Prism Central/Element access
          private: no
        - name: prism_password
          prompt: Enter the password for the above user
          unsafe: yes
          private: yes
      tasks:
        - name: Include variables
          include_vars: dir=vars
        - name: Use ansible role nutanix_vm_create
          include_role:
            name: nutanix_vm_create

**NOTE:** In the above example playbook, **vars_prompt** is used to prompt the user for Prism credentials. If you opt to
use a vault for these credentials or accept them via other means, ensure that they are stored in the **prism_user** and **prism_password** variables.


*STEP 4:* Exceute the playbook as per the following example:

    ansible-playbook main.yml


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