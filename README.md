# Ansible-Playbook for Mikrotik RouterBoard Backup

## English

### Description

This Ansible playbook automates the backup process for Mikrotik Router Board devices. It creates a temporary RAM disk (tmpfs) to minimize writes to the flash memory, thereby prolonging the flash storage's lifetime. The playbook uses an inventory file to define the IP addresses and login information of your devices. An example file (`inventory.ini.example`) is provided and should be renamed to `inventory.ini` before use.

### Open Taks

- Externalize variable definitions into the ini file so that `backup.yml` does not need to be modified.
- Clean up the backup path based on a retention time.

### Usage

1. Rename `inventory.ini.example` to `inventory.ini` and update it with your devices' IP addresses and login information.
2. Run the playbook with the following command:
   ```bash
   ansible-playbook backup.yml -i inventory.ini

## Deutsch

### Beschreibung

Dieses Ansible-Playbook automatisiert den Backup-Prozess für Mikrotik RouterBoard Geräte. Es erstellt eine temporäre RAM-Disk (tmpfs), um Schreibzugriffe auf den Flash-Speicher zu minimieren und somit die Lebensdauer des Flash-Speichers zu verlängern. Das Playbook verwendet eine Inventardatei, in der die IP-Adressen und Login-Informationen deiner Geräte definiert sind. Eine Beispieldatei (`inventory.ini.example`) ist enthalten und sollte vor der Verwendung in `inventory.ini` umbenannt werden.

### Offene Aufgaben

- Variablendefinitionen in die ini-Datei auslagern, sodass `backup.yml` nicht angepasst werden muss.
- Bereinigung des Backup-Pfades anhand einer Aufbewahrungsdauer.

### Nutzung

1. Benenne `inventory.ini.example` in `inventory.ini` um und passe die Datei mit den IP-Adressen und Login-Informationen deiner Geräte an.
2. Führe das Playbook mit folgendem Befehl aus:
   ```bash
   ansible-playbook backup.yml -i inventory.ini
