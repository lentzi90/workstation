# README

Fedora:

```bash
sudo dnf install git ansible
```

Ubuntu:

```bash
sudo apt install git ansible
```

Common:

```bash
git clone https://github.com/lentzi90/workstation.git
cd workstation
ansible-playbook -i inventory.ini setup.yml -K
```
