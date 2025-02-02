# HamWAN Infrastructure Configs
This repository hosts a group of ansible playbooks to deploy and manage HamWAN infrastructure.

## Usage
On the machine that you plan to execute ansible from, do the following:
```bash
apt-add-repository ppa:ansible/ansible
apt-get update
apt-get install python-pip python-netaddr ansible git software-properties-common -y
pip install dnspython
ansible-galaxy install yaegashi.blockinfile
ansible-galaxy install angstwad.docker_ubuntu
git clone https://github.com/HamWAN/infrastructure-configs
```

You'll need to create a directory inside the "locales" folder with a few items unique to your deployment. We recommend that you place private information in a vault; in our examples below, we utilize the "vault-password-file" argument to make the experience uninteractive.

Here's an example of configuring a new VM and then setting it up for sensu. Note that the user provided for the first command matches whatever you had configured during OS install, but then for the next command it uses your local user and key.
```bash
ansible-playbook -i locales/memphis/hosts.sh linux_setup.yml -u ryan_turner -k -K -s --vault-password-file ~/.vault_pass.txt -vvvv --limit eden.mno.memhamwan.net
ansible-playbook -i locales/memphis/hosts.sh sensu_client.yml -s --vault-password-file ~/.vault_pass.txt -vvvv --limit eden.mno.memhamwan.net
ansible-playbook -i locales/memphis/hosts.sh chat.yml -s --vault-password-file ~/.vault_pass.txt -vvvv --limit chat.mno.memhamwan.net
ansible-playbook -i locales/memphis/hosts.sh prometheus.yml -s --vault-password-file ~/.vault_pass.txt -vvvv --limit monitor.mno.memhamwan.net
```
The first command updates the routers and does some basic universal configuration; the second one then looks for each core router, sector, and ptp and configures them with their appropriate settings.

Let's setup field day! For memhamwan, that means a core router, three APs for general public use, and two PtP links. The expectation is that the wireless interface of the PtP link will be manually configured, since that's site specific.
```base
ansible-playbook -i locales/memphis/hosts.sh field_day.yml --vault-password-file ~/.vault_pass.txt -vvvv -u ryan_turner
```

### SSH to servers
Since we use cert auth everywhere, you'll need to have SSH'd to that remote server at least once and have your cert installed locally for connecting to that server. Also, if your local use is different than your remote user, be sure to add -u remote_user to your ansible-playbook command!

## Configuring a new appliance
* First, make sure you have the router on your LAN and with an IP and gateway (can it ping google.com?)
* Second, in your ansible-playbook command, add the following: ```--extra-vars "ansible_host=[local-ip]"```; make sure that you limit the command to only run on one host at a time!

## License
Refer to LICENSE.md
