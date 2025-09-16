# Static Web Test
This project was created as a basic testing web for subsequent deployment using Ansible. It includes a simple HTML/CSS structure that was used to verify the functionality of automated deployment.
> This project is also available in the Czech version: [README.md](README.md)

---
## Technology
- HTML
- CSS
- Replit
- Ansible (in the following project)

---
## Project: Ansible Web Server Setup
This project automates the configuration of a Linux server (Ubuntu 22.04) using Ansible. The goal is to create a simple web service (NGINX) and set it up securely for operation.

---
## Goals
- Create an Ansible project according to best practices
- Set up a web server (NGINX) with custom configuration
- Deploy an HTML page from a Git repository or alternatively using a template.
- Ensure idempotent behavior and ownership of files
- Service operation on port `80` with a custom folder for content
- Ensure safe operating settings

---
## Project structure
kořenová složka `static-web-test/`:
- inventory/hosts.ini 
- group_vars/web.yml
- group_vars/web_secrets.yml (simulace citlivých dat)
- playbooks/webserver.yml
- roles/users/tasks/main.yml
- roles/webserver/tasks/main.yml
- roles/webserver/handlers/main.yml
- roles/webserver/templates/index.html.j2 (zakomentován - Git nahrazuje)
- roles/webserver/templates/nginx.conf.j2
- roles/webserver/vars/main.yml
- roles/firewall/tasks/main.yml
- roles/ssh/tasks/main.yml
- roles/ssh/handlers/main.yml
- roles/updates/tasks/main.yml
- roles/updates/handlers/main.yml
- README.md
- README-en.md
- LICENSE

---
## Project launch
```bash
ansible-playbook -i inventory/hosts.ini playbooks/webserver.yml
```

---
## Web content from GitHub
- The webpage `index.html` is downloaded from the public GitHub repository: `https://github.com/Miska296/static-web-test`
- The download is carried out using the `git` module in the role of `webserver`.
- The content is automatically placed in the folder `/opt/static-sites`.
- The ownership of the folder is set to the user `webapp`.
- The local template `index.html.j2` has been commented out and replaced with real content.

---
## Deployment of the service
- NGINX uses its own configuration from the file `nginx.conf.j2`.
- The web is running on port `80`
- Configuration and files are checked idempotently.
- The service restart only occurs when there is a change.
- The content owner is the user `webapp` (automatically created)

---
## Idempotence
- Installation of packages via `apt` only if they are missing
- Configuration files via `template` with notification
- Restart services only when there is a change (`handlers`)
- Variables are defined in `group_vars/` and `vars/` for clarity.
- The `file` module for checking ownership, rights, and directory structure

---
## Firewall (UFW)
- Installation of the `ufw` tool
- Allowing ports `22` (SSH) and `80` (HTTP)
- Blocking other incoming connections
- Activation of the firewall using the `firewall` role
- The module `community.general.ufw` is part of the `community.general` collection. Installation on a real server:
  ```bash
  ansible-galaxy collection install community.general
  ```

---
## User and permissions
- Dedicated user `webapp` created
- The web files in `/opt/static-sites` are his property.
- NGINX processes run under this user (`user webapp;` in `nginx.conf.j2`)
- The requirement for a safe operational setup of the service has been met.

---
## SSH security
- Login as `root` has been disabled (`PermitRootLogin no`)
- Login using a password has been disabled (`PasswordAuthentication no`).
- SSH access is possible **only using an SSH key**.
- Configuration done using the `ssh` role.
- Changes in `sshd_config` automatically trigger a restart of the service.

---
## Update and protection
- Automatic security updates using `unattended-upgrades`
- Configuration via the `20auto-upgrades` file
- Installed and run `fail2ban` for protection against `brute-force` attacks.
- The `fail2ban` service protects SSH access.

---
## Functionality validation
- At the end of the playbook, an HTTP test is performed using the `uri` module.
- It is being verified that the server responds with the code `200 OK`.
- It is checked that the response contains the expected text (for example, "Hello from GitHub!")
- In case of an error, the playbook will terminate with a message.

---
## Sensitive variables with Ansible Vault (simulation)
- The project allows for the secure storage of sensitive values using `ansible-vault`.
Examples of data:
- SSH keys (`ssh_private_key` )
- Credentials (`db_password`, `api_token`)
Creation of an encrypted file (simulation):
  ```bash
  ansible-vault create group_vars/web_secrets.yml
  ```
In Replit, the folder `/opt/static-sites` is not created automatically — Ansible will create it during the actual deployment.

---
## Bonus points
- Simulated storage of sensitive data using `ansible-vault` in the encrypted file `web_secrets.yml`
- Web content downloaded from the GitHub repository using the `git` module
- The project utilizes security features, automation, and monitoring

---
## Testing and recommendations
Project tested locally in Replit:
- Validated playbook syntax
- Loading inventory and variables
- Validation using HTTP test
For real deployment, I recommend a Linux VM with Ubuntu 22.04 (e.g. Hetzner, Oracle Cloud, VirtualBox).

---
## How to contribute
I would welcome suggestions for improving or expanding the project. You can open an `issue` or a `pull request`.

---
## Author
The project was prepared by [Michaela Kučerová](https://github.com/Miska296)  
Version: 1.0  
Date: July 2025

---
## License
This project is available under the MIT license. See the file for details [LICENSE](LICENSE).
