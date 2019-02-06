# Ansible-Proxmox-inventory

## About

Proxmox dynamic inventory for Ansible. Based on [original plugin](https://raw.githubusercontent.com/ansible/ansible/devel/contrib/inventory/proxmox.py) from Mathieu Gauthier-Lafaye and [updated plugin](https://github.com/xezpeleta/Ansible-Proxmox-inventory) by Xabi Ezpeleta


### Requirements

installed qemu-guest-agent on proxmox vm's

### Features

- **Removed ansible lib requirements**
- **Requests instead of urllib**
- **Qemu interfaces ip detection**: You should have [qemu-guest-agent](https://pve.proxmox.com/wiki/Qemu-guest-agent) installed and activated 
- **ProxmoxVE cluster**: if your have a ProxmoxVE cluster, it will gather the whole VM list from your cluster
- **Advanced filtering**: you can filter the VM list based in their status or a custom tag included in the `Notes` field

## Instructions

Download **proxmox.py**

```sh
sudo chmod +x proxmox.py
```

Let's test it:

```sh
python /etc/ansible/proxmox.py \
  --url=https://<your-proxmox-url>:8006/ \
  --username=<proxmox-username> \
  --password=<proxmox-password> \
  --trust-invalid-certs \
  --qemu_interface=ens18 \
  --list --pretty
```

If you get a list with all the VM in your Proxmox cluster, everything is ok.

You may also save your settings in a JSON file with the same name of the Python script, in same folder (e.g.: if the downloaded script is `proxmox.py`, the configuration file will be `proxmox.json`): 

```json
{
    "url": "https://10.0.0.1:8006/",
    "username": "apiuser@pam",
    "password": "apiuser1234",
    "validateCert": false,
    "qemu_interface": "ens18"
}
```

you can include the dynamic inventory in your ansible commands:

```sh
# Ping: connect to all VM in Proxmox using root user
ansible -i /etc/ansible/proxmox.py all -m ping -u root
```

#$ Added support for using the Notes field of a VM to define groups and variables:
> A well-formatted JSON object in the Notes field will be added to the _meta
> section for that VM.  In addition, the "groups" key of this JSON object may be
> used to specify group membership:

For instance, you can use the following JSON code in a VM host notes:

```json
{ "groups": ["windows"] }
```

So if you want to exclude Windows machines, you could do the following:

```sh
# Run a playbook in every running Linux machine in Proxmox
ansible-playbook -i ./proxmox.py --limit='running,!windows' playbook-example/playbook.yml
```
