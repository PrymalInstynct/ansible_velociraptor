# ansible-velociraptor
Ansible role for Installation and Configuration of [Velocidex Velociraptor](https://github.com/Velocidex/velociraptor)

## OS Platforms

This role has been tested on the following operating systems:

- Ubuntu 18.04, 20.04 (server,client)
- Red Hat Enterprise Linux 7, 8 (client)
- Windows 10 (client)
- MacOSX Intel & M1 (client)

## Role Variables
| Variable | Location | Default Value | Notes |
| -------- | -------- | ------------- | ----- |
| velociraptor_client | defaults/main.yml | `false` | Identifies nodes which should receive the Velociraptor Client |
| velociraptor_server | defaults/main.yml | `false` | Identifies nodes which should receive the Velociraptor Server |
| velociraptor_version | defaults/main.yml | `v0.6.1` | What Velocirapter version to install |
| velociraptor_admin_user | defaults/main.yml | `admin` | Admin Username for WebGui |
| velociraptor_admin_password | defaults/main.yml | `vault_velociraptor_admin_password` | Referenced Vaulted Varible that sets the Admin Password for the WebGui |
| vault_velociraptor_admin_password | vars/vault.yml | `changeme` | Sets the Admin Password for the WebGui |
| velociraptor_url | vars/{{ ansible_os_family }}-{{ ansible_architecture }}.yml | `https://github.com/Velocidex/velociraptor/releases/download/{{ velociraptor_version }}/velociraptor-{{ velociraptor_version }}-linux-amd64` | URL for Release to Install |
| velociraptor_dir | vars/{{ ansible_os_family }}-{{ ansible_architecture }}.yml | `/opt/velociraptor` | Directory to install Velociraptor into |
| velociraptor_config_dir | vars/{{ ansible_os_family }}-{{ ansible_architecture }}.yml | `/etc/velociraptor` | Directory to install Velociraptor Config into |
| velociraptor_msi | vars/{Windows-{{ ansible_architecture }}.yml | `velociraptor-{{ velociraptor_version }}-windows-386.msi` | Windows Installer MSI |
| _velociraptor_windows_dir | vars/{Windows-{{ ansible_architecture }}.yml | `C:\Program Files (x86)\Velociraptor\` | Windows MSI Install directory |

## Prerequisite Steps

Update inventory.yml # group_vars/* to include the server and client information needed for the engagement

If installing client on a Windows host the ansible control node will need

```
$ pip install "pywinrm>=0.2.2"
## or 
$ pip3 install --user pywinrm
```

WinRM will also need to be configured on each Windows host Run Powershell as an Administrator
```
$url = "https://raw.githubusercontent.com/ansible/ansible/devel/examples/scripts/ConfigureRemotingForAnsible.ps1"
$file = "$env:temp\ConfigureRemotingForAnsible.ps1"
(New-Object -TypeName System.Net.WebClient).DownloadFile($url, $file)
powershell.exe -ExecutionPolicy ByPass -File $file
```

Change the Velociraptor admin password in vars/vault.yml

`ansible-vault encrypt vars/vault.yml`

## Usage

### Nix Hosts
Install the Server and Clients on Nix based systems:

```
ansible-playbook -i inventory.yml -l nix nix-velociraptor-playbook.yml --ask-vault
```

Install the Server on Nix based systems:

```
ansible-playbook -i inventory.yml -l velociraptor_servers nix-velociraptor-playbook.yml --ask-vault
```

Install the Clients on Nix based systems:

```
ansible-playbook -i inventory.yml -l velociraptor_clients nix-velociraptor-playbook.yml --ask-vault
```

Uninstall the Server and Clients on Nix based systems:
```
ansible-playbook -i inventory.yml nix-velociraptor-playbook.yml --ask-vault --tag uninstall
```

Uninstall the Server on Nix based systems:
```
ansible-playbook -i inventory.yml nix-velociraptor-playbook.yml --ask-vault --tag uninstall_server
```

Uninstall the Clients on Nix based systems:
```
ansible-playbook -i inventory.yml nix-velociraptor-playbook.yml --ask-vault --tag uninstall_client
```

### Windows Hosts
Install the Clients on Windows based systems:

```
ansible-playbook -i inventory.yml -l windows win-velociraptor-playbook.yml --ask-vault
```

Uninstall the Clients on Windows based systems:
```
ansible-playbook -i inventory.yml win-velociraptor-playbook.yml --ask-vault --tag uninstall_client
```

## Known Issues

* As velociraptor is generating the server config file (because of secrets), idempotence check is based on file existence. If you change settings after initial setup, you must remove the existing config file before running again playbook. It will also regenerate secrets including certificates, meaning playbook must be re-executed on all agents too. Alternatively, you can directly edit settings on velociraptor server and when necessary, clients.

## License

[MIT](LICENSE)
