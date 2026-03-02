# README

**Fedora Silverblue**

No extra steps needed.

**Ubuntu:**

```bash
sudo apt install git python3.10-venv
```

Common:

```bash

python3 -m venv .venv
source .venv/bin/activate
pip install ansible jmespath

ansible-galaxy collection install community.general
git clone https://github.com/lentzi90/workstation.git
cd workstation
ansible-playbook setup.yml -K # -e github_token=${GITHUB_TOKEN}
```

## Install extras

Not everything is installed by default (e.g. `pre-commit`, `syncthing`, `ollama`, extra flatpaks).
To install all extras, use the `extras` tag:

```bash
ansible-playbook setup.yml -K --tags "extras"
```

You can also install a specific extra by using its tag directly:

```bash
ansible-playbook setup.yml -K --tags "ollama"
```

## Configure VS Code flatpak access to docker

The playbook already configures access to the docker socket, but the docker executable is not in the PATH.
Go to *File > Preferences > Settings > Extensions Dev Containers* and set the Docker Path to

```
/run/host/usr/bin/docker
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
