# README

**Fedora Silverblue**

No extra steps needed.

**Ubuntu:**

```bash
sudo apt install git python3.10-venv
```

Common:

```bash

python -m venv .venv
source .venv/bin/activate
pip install ansible jmespath

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

## Set git signing key and email

```bash
git config --global user.email <email>
git config --global user.signingKey <key>
git config --global commit.gpgsign true
```

## Setup passwordless login and sudo using pam-u2f

The playbooks will install the necessary packages, but not enable this by default due to the sensitivity of it.

```bash
mkdir ~/.config/Yubico
pamu2fcfg > ~/.config/Yubico/u2f_keys
# Fedora: Use authselect to enable pam-u2f
sudo authselect enable-feature with-pam-u2f
# Ubuntu: Edit common-auth to enable pam-u2f
sudo vim /etc/pam.d/common-auth
auth        sufficient                                   pam_u2f.so cue
```

P.S. There are some [udev rules that may be needed](https://support.yubico.com/hc/en-us/articles/360013708900-Using-Your-U2F-YubiKey-with-Linux).
