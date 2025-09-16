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
## Aktualizace a ochrana
- Automatické bezpečnostní aktualizace pomocí `unattended-upgrades`
- Konfigurace přes soubor `20auto-upgrades`
- Nainstalován a spuštěn `fail2ban` pro ochranu proti `brute-force` útokům
- Služba `fail2ban` chrání SSH přístup

---
## Validace funkčnosti
- Na konci playbooku je proveden HTTP test pomocí modulu `uri`
- Ověřuje se, že server odpovídá s kódem `200 OK`
- Kontroluje se, že odpověď obsahuje očekávaný text (např. „Hello from GitHub!“)
- V případě chyby se playbook ukončí s hlášením

---
## Citlivé proměnné s Ansible Vault (simulace)
- Projekt umožňuje bezpečné uchování citlivých hodnot pomocí `ansible-vault`.
Příklady dat:
- SSH klíče (`ssh_private_key`)
- Hesla (`db_password`, `api_token`)
Vytvoření šifrovaného souboru (simulace):
  ```bash
  ansible-vault create group_vars/web_secrets.yml
  ```
V Replitu se složka `/opt/static-sites` nevytváří automaticky — Ansible ji vytvoří při reálném nasazení.

---
## Bonusové body
- Simulované uložení citlivých dat pomocí `ansible-vault` v šifrovaném souboru `web_secrets.yml`
- Webový obsah stažen z GitHub repozitáře pomocí modulu `git`
- Projekt využívá bezpečnostní prvky, automatizaci i monitoring

---
## Testování a doporučení
Projekt testován lokálně v Replitu:
- Ověřena syntaxe playbooku
- Načtení inventáře a proměnných
- Validace pomocí HTTP testu
Pro reálné nasazení doporučuji Linux VM s Ubuntu 22.04 (např. Hetzner, Oracle Cloud, VirtualBox).

---
## Jak přispět
Ráda uvítám návrhy na vylepšení nebo rozšíření projektu. Můžete otevřít `issue` nebo `pull request`.

---
## Autor
Projekt vypracovala [Michaela Kučerová](https://github.com/Miska296)  
Verze: 1.0  
Datum: červenec 2025

---
## Licence
Tento projekt je dostupný pod licencí MIT. Podrobnosti viz soubor [LICENSE](LICENSE).
