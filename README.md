# VMware Direct/Indirect Node Counting

### Setup Environment

This requires the latest version of vmware.vmware; the [main](https://github.com/ansible-collections/vmware.vmware) branch can be used:

1. Install python requirements: `pip install -r https://github.com/ansible-collections/vmware.vmware/blob/main/requirements.txt`
2. Install the collection: `ansible-galaxy collection install git@github.com:ansible-collections/vmware.vmware.git --force` or you can install the collections/requirements.yml file located in this repo

### Setup Variables

You can either expose the vcenter credentials as environment variables, or set them in your copy of `vars.yml`. If you are using AAP, you can create a credential of type VMware and use that in your job templates and inventory instead.

You also must set the `vcenter_resource_prefix` variable, either in `vars.yml` or by passing it to each playbook at runtime. This will be a string that helps group and identify the resources you are creating for this test. For example,I could use my name and last initial, `mikem`

### Setup VM and Resources

You can create a vm using the `setup.yml` playbook. To run:
```
ansible-playbook setup.yml
# or
ansible-playbook setup.yml -e 'vcenter_resource_prefix=mikem'
```

This creates a folder, a RHEL 9 VM inside the folder, and outputs a dynamic inventory config that will allow you to connect to the host.

### Compare Direct and Indirect Results

Part of the `setup.yml` playbook prints an inventory config to the output. You should copy that config to an inventory file (or AAP inventory). If you're running locally, the playbook will have already done this for you and written it to `{{ playbook_dir }}/hosts.vms.yml`. <br>

To compare direct and indirect results:
1. You can sync the inventory in AAP or test it using `ansible-inventory -i hosts.vms.yml --list`. This will show the direct node inventory
2. You can run the `compose.yml` playbook. This will run the `vmware.vmware.guest_info` module to generate indirect node data. It will also run a ping task against the host if the inventory is used.
For example:
```
# Just generate indirect data
ansible-playbook compare.yml
# or
ansible-playbook compare.yml -e 'vcenter_resource_prefix=mikem'

# Generate indirect data, and ping the host using the inventory file
ansible-playbook compare.yml -i hosts.vms.yml
# or
ansible-playbook compare.yml -i hosts.vms.yml -e 'vcenter_resource_prefix=mikem'

```


### Destroy VM and Resources

When you're done testing, you can destroy resources using the `teardown.yml` playbook. To run:
```
ansible-playbook teardown.yml
# or
ansible-playbook teardown.yml -e 'vcenter_resource_prefix=mikem'
```
