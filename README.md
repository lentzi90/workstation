# README

Fedora:

```bash
sudo dnf install git ansible
```

Fedora Silverblue

```bash
rpm-ostree install ansible
systemctl reboot
```

Ubuntu:

```bash
sudo apt install git ansible
```

Common:

```bash
ansible-galaxy collection install community.general
git clone https://github.com/lentzi90/workstation.git
cd workstation
ansible-playbook -i inventory.ini setup.yml -K
```

## Install extras

Not everything is installed by default.
To install everything, add `-e install_extras=true`.

## Get latest versions

Check what the latest versions of some binaries are:

```bash
ansible-playbook -i inventory.ini setup.yml --tags=versions
```

This will generate the file `versions.yaml` with version numbers that can be used to set the versions you want to freeze.
