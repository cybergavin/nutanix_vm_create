Ansible Role Name - cybergavin.nutanix_vm_create
================================================

This ansible role enables the creation of one or more VMs on a single Nutanix cluster.


Features
---------

- Allows provisioning of VMs from different disk images
- Allows provisioning of VMs in different subnets
- Generates an inventory file with the IP addresses of the VMs that were created, for subsequent use by other playbooks
- If enabled, mounts and enables Nutanix Guest Tools on the VMs after provisioning.

**NOTE:**
- Creates VMs with **UEFI** boot config by default. This may be overriden globally in the vars/main.yml variables file.


Requirements
------------

- A user-provided comma-separated-values file containing the specifications of one or more VMs. Refer the sample files/vm.csv in the role directory.
- A user-provided variables file containing appropriate values for required variables.


Role Variables
--------------

- pc_cluster_fqdn   # FQDN/IP for Prism Central
- cluster_name      # Name of the nutanix cluster on which VMs will be created 
- vm_data_csv       # Relative path and name of the comma-separated-values file containing details of the VMs to be deployed
- global_debug      # Global Debug flag (boolean)
- enable_ngt        # Enable Nutanix Guest Tools? (boolean)
- boot_type         # Boot Type for all VMs - "UEFI" (default), "LEGACY" or "SECURE_BOOT"


Dependencies
------------

None


Tested Environment:
-------------------

- Ansible 2.9
- Nutanix AOS 5.15.4
- AHV 20190916.360


Example usage after installation of the ansible role
-----------------------------------------------------

*STEP 1:* Create a **vars/main.yml** file as per the following example:

    pc_cluster_fqdn: "prismcentral.contoso.com"   # FQDN/IP for Prism Central
    cluster_name: "mynutanix"                     # Name of the nutanix cluster on which VMs will be created
    vm_data_csv: "files/vm.csv"                   # Relative path and name of the comma-separated-values file containing details of the VMs to be deployed
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
          prompt: Enter a username for Prism Central access
          private: no
        - name: prism_password
          prompt: Enter the password for the above user
          unsafe: yes
          private: yes
      tasks:
        - name: Include variables
          include_vars: dir=vars
        - name: Use ansible role cybergavin.nutanix_vm_create
          include_role:
            name: cybergavin.nutanix_vm_create

**NOTE:** In the above example playbook, **vars_prompt** is used to prompt the user for Prism credentials. If you opt to
use a vault for these credentials or accept them via other means, ensure that they are stored in the **prism_user** and **prism_password** variables.


*STEP 4:* Execute the playbook as per the following example:

    ansible-playbook main.yml


**NOTE:**
- An inventory file **files/inventory** will be generated containing the IP addresses of the VMs that were created
- If you wish to enable NGT, you may also set "enable_ngt: true" in **vars/main.yml**



Author Information
------------------

Cybergavin - https://cybergav.in - https://github.com/cybergavin


References
-----------

- Influenced/Inspired by the Ansible role mbach04.nutanix_vm_provisioner at https://github.com/mbach04/nutanix_vm_provisioner.
- The ansible role mbach04.nutanix_vm_provisioner is licensed using the MIT License with Copyright (c) 2018 Red Hat.
