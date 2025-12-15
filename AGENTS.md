# AGENTS.md

## Repository Overview

This is an Ansible playbook repository for automated workstation setup and configuration. It supports Fedora Silverblue and Ubuntu/Debian distributions.

## Key Files

- `setup.yml` - Main playbook that orchestrates the setup
- `inventory.ini` - Ansible inventory file
- `requirements.yml` - Ansible Galaxy requirements
- `ansible.cfg` - Ansible configuration

## Repository Structure

```
workstation/
├── roles/
│   ├── base/          # Base system packages and configuration
│   ├── facts/         # System facts gathering
│   └── workstation/   # Workstation-specific tools and applications
├── setup.yml          # Main playbook
└── README.md          # Setup instructions
```

## Common Tasks

### Running the Playbook

```bash
ansible-playbook setup.yml -K
```

### Install Everything (Including Extras)

```bash
ansible-playbook setup.yml -K -e install_extras=true
```

### Run Specific Tags

```bash
ansible-playbook setup.yml -K --tags "docker,kubectl"
```

## Available Tags

Common tags include:
- `base` - Base system setup
- `workstation` - Workstation tools
- `docker` - Docker installation (apt on Ubuntu/Debian, sysext on Silverblue)
- `sysext` - Systemd-sysext installations (Silverblue only)
- `binaries` - CLI tools (kubectl, helm, kind, etc.)
- `flatpaks` - Flatpak applications
- `extras` - Optional components

## What This Playbook Installs

### Base Tools
- Standard CLI utilities (git, vim, htop, etc.)
- Development tools (gcc, build essentials)
- Security tools (GPG, U2F support)

### Development Environment
- Docker (via apt on Ubuntu/Debian, via sysext on Silverblue)
- Kubernetes tools (kubectl, kind, minikube, helm, kustomize, k9s)
- Cloud-native tools (argocd, tilt, clusterctl)
- Programming languages (Go, Rust)
- Version managers and package managers

### Applications
- Flatpak applications
- AppImages (Warp, DevPod, OpenCloud)
- Chrome browser

## Installation Methods

### Sysext Overlays (Fedora Silverblue)

For Fedora Silverblue and other immutable operating systems, some software is installed as systemd-sysext overlays. This provides several advantages:

- No package layering required (faster updates, less disk space)
- Extensions can be easily updated or removed
- The base system remains unchanged
- Reboot-less installation and updates

**Currently supported sysext installations:**
- `moby-engine` (Docker) - Available via the `docker` tag

The common sysext installation mechanism is implemented in `roles/workstation/tasks/common/sysext.yml` and handles:
1. Creating required directories and SELinux contexts
2. Downloading sysext configurations from https://extensions.fcos.fr/fedora
3. Installing the extension using systemd-sysupdate
4. Merging extensions into the system

For detailed information about sysext installations, see `docs/SYSEXT.md`.

#### Adding New Sysext Installations

To add a new sysext, create a task file that includes the common `sysext.yml` task with these variables:
- `sysext_name` - Name of the sysext (required)
- `sysext_base_url` - Base URL for configurations (optional, defaults to Fedora extensions)
- `sysext_post_install_tasks` - Additional tasks to run after installation (optional)

### GitHub Binary Installation

Many CLI tools are installed directly from GitHub releases. The playbook uses a common pattern to:

1. Query the GitHub API for the latest release version
2. Check if the tool is already installed and compare versions
3. Download and install only if needed or if a newer version is available

This mechanism is implemented in `roles/workstation/tasks/common/github_release.yml` and is used by most binary installation tasks. The playbook is idempotent - it will skip downloads if the correct version is already installed.

### Adding New GitHub Binaries

To add a new binary from GitHub releases, create a task file that includes the common `github_release.yml` task with these variables:
- `binary_name` - Name of the binary
- `repo` - GitHub repository (e.g., `owner/repo`)
- `version_check_command` - Command to check installed version (optional)
- `version_pattern` - Regex to extract version number (optional)

## Configuration Variables

- `install_extras` - Set to `true` to install optional components (default: `false`)
- `github_token` - Optional GitHub token for authenticated downloads

## Platform Support

- **Fedora Silverblue**: Uses `rpm-ostree` for package management and systemd-sysext for overlays
- **Ubuntu/Debian**: Uses `apt` package manager

## Important Notes

- The playbook is idempotent - safe to run multiple times
- Most tasks require sudo access (use `-K` flag)
- Some installations download the latest releases from GitHub
- Flatpak and Docker configurations are handled automatically