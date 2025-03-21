# Ansible Mikrotik Router Board Backup Playbook

## Description / Beschreibung

**English:**  
This Ansible playbook automates the backup process for Mikrotik Router Board devices. It creates a temporary RAM disk (tmpfs) to minimize writes to the flash storage. The inventory file (`inventory.ini`) contains the IP addresses and login information of your devices. An example file (`inventory.ini.example`) is provided and should be renamed to `inventory.ini` before running the playbook.

**Deutsch:**  
Dieses Ansible Playbook automatisiert den Sicherungsprozess für Mikrotik Router Board Geräte. Es erstellt eine temporäre RAM-Disk (tmpfs), um zu verhindern, dass der Flash-Speicher ständig beschrieben wird. In der Inventardatei (`inventory.ini`) werden die IP-Adressen und Login-Informationen der Geräte definiert. Eine Beispiel-Datei (`inventory.ini.example`) ist enthalten und sollte vor der Ausführung des Playbooks in `inventory.ini` umbenannt werden.

## Open Points / Offene Punkte

**English:**  
- Define variables externally in the ini file so that `backup.yml` does not need to be modified.

**Deutsch:**  
- Variablen außerhalb des Playbooks in der ini-Datei definieren, damit `backup.yml` nicht geändert werden muss.

## Usage / Nutzung

**English:**  
1. Rename `inventory.ini.example` to `inventory.ini` and update it with your devices' IP addresses and login information.  
2. Run the playbook with the following command:  
   ```bash
   ansible-playbook backup.yml -i inventory.ini
