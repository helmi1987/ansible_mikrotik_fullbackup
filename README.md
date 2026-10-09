# Ansible-Playbook for MikroTik RouterOS Backup

## English

### Description

This Ansible playbook creates a full backup of MikroTik RouterOS devices. All commands are executed from the Ansible control node via `sshpass` + `ssh`/`scp` (`delegate_to: localhost`); no RouterOS Ansible modules are used.

To minimize writes to the flash memory, a temporary RAM disk (`tmpfs`, max. 1 MB, slot `tmpBackup`) is created on the device. The backup files are written there, copied to the control node and the RAM disk is removed afterwards.

Per device, two files are created:

| File | Content |
|------|---------|
| `<identity>__ROS-<version>__<model>.backup` | Binary backup (`/system backup save`) |
| `<identity>.rsc` | Text export incl. sensitive data (`/export show-sensitive`) |

Values for the filename:

- `identity`: `/system identity print` → `name` (fallback: inventory hostname)
- `version`: `/system resource print` → `version`, first token only (e.g. `7.15.3`; fallback: `unknown`)
- `model`: `/system routerboard print` → `model` (fallback: `/system resource print` → `board-name`, then `x86`)

The fallbacks cover x86/CHR installations without RouterBOARD information.

### Directory structure

```
<backup_path>/<inventory_hostname>/<YYYY>/<MM-Month>/<YYYY-MM-DD>/
```

Dates are UTC.

### Requirements

Control node:

- Ansible
- `sshpass`, `ssh`, `scp`

MikroTik device:

- RouterOS 7 (`/disk add type=tmpfs` and `/export show-sensitive` are RouterOS 7 syntax)
- SSH service enabled, user with permissions for `/system backup`, `/export`, `/disk` and `/file`

`requirements.yml` lists `ansible.netcommon`, `ansible.utils` and `community.routeros`. They are currently not used by `backup.yml`.

### Configuration

Copy `inventory.ini.example` to `inventory.ini` and adjust it:

```ini
[mikrotik]
192.168.88.1 ansible_ssh_pass='mySecurePassword'

[mikrotik:vars]
backup_path=/tmp/backup
ansible_user=admin
ansible_port=22
```

| Variable | Description |
|----------|-------------|
| `ansible_ssh_pass` | SSH password of the device |
| `ansible_user` | SSH user |
| `ansible_port` | SSH port |
| `backup_path` | Base directory for backups on the control node |

`*.ini` is listed in `.gitignore`, so `inventory.ini` is not committed.

### Usage

```bash
ansible-playbook backup.yml -i inventory.ini
```

### Process

1. Read RouterBOARD info, identity and system resources
2. Build filename and target path
3. Create RAM disk `tmpBackup` (errors ignored, e.g. if it already exists)
4. Remove existing files in `tmpBackup` (errors ignored)
5. Create `.backup` and `.rsc` in `tmpBackup`
6. Create target directory on the control node
7. Copy both files via `scp`
8. Fail if one of the files is missing locally
9. Remove RAM disk `tmpBackup` (errors ignored)
10. List files on the device (`/file print`); the output is registered only, not printed

### Known limitations

- `StrictHostKeyChecking=no` is used for all SSH/SCP connections.
- The password is passed on the command line (`sshpass -p`) and is visible in the process list; `no_log` is not set.
- RAM disk is limited to 1 MB (`tmpfs-max-size=1M`); larger backups fail.

### Open tasks

- Clean up the backup path based on a retention time.

---

## Deutsch

### Beschreibung

Dieses Ansible-Playbook erstellt ein vollständiges Backup von MikroTik-RouterOS-Geräten. Alle Befehle werden vom Ansible-Control-Node über `sshpass` + `ssh`/`scp` ausgeführt (`delegate_to: localhost`); es werden keine RouterOS-Ansible-Module verwendet.

Um Schreibzugriffe auf den Flash-Speicher zu minimieren, wird auf dem Gerät eine temporäre RAM-Disk (`tmpfs`, max. 1 MB, Slot `tmpBackup`) erstellt. Die Backup-Dateien werden dort abgelegt, auf den Control-Node kopiert und die RAM-Disk anschliessend entfernt.

Pro Gerät werden zwei Dateien erstellt:

| Datei | Inhalt |
|-------|--------|
| `<identity>__ROS-<version>__<model>.backup` | Binäres Backup (`/system backup save`) |
| `<identity>.rsc` | Text-Export inkl. sensitiver Daten (`/export show-sensitive`) |

Werte für den Dateinamen:

- `identity`: `/system identity print` → `name` (Fallback: Inventory-Hostname)
- `version`: `/system resource print` → `version`, nur erster Teil (z. B. `7.15.3`; Fallback: `unknown`)
- `model`: `/system routerboard print` → `model` (Fallback: `/system resource print` → `board-name`, danach `x86`)

Die Fallbacks decken x86/CHR-Installationen ohne RouterBOARD-Informationen ab.

### Verzeichnisstruktur

```
<backup_path>/<inventory_hostname>/<YYYY>/<MM-Monat>/<YYYY-MM-DD>/
```

Datumswerte sind UTC.

### Voraussetzungen

Control-Node:

- Ansible
- `sshpass`, `ssh`, `scp`

MikroTik-Gerät:

- RouterOS 7 (`/disk add type=tmpfs` und `/export show-sensitive` sind RouterOS-7-Syntax)
- SSH-Dienst aktiv, Benutzer mit Rechten für `/system backup`, `/export`, `/disk` und `/file`

`requirements.yml` listet `ansible.netcommon`, `ansible.utils` und `community.routeros`. Diese werden von `backup.yml` aktuell nicht verwendet.

### Konfiguration

`inventory.ini.example` nach `inventory.ini` kopieren und anpassen:

```ini
[mikrotik]
192.168.88.1 ansible_ssh_pass='mySecurePassword'

[mikrotik:vars]
backup_path=/tmp/backup
ansible_user=admin
ansible_port=22
```

| Variable | Beschreibung |
|----------|--------------|
| `ansible_ssh_pass` | SSH-Passwort des Geräts |
| `ansible_user` | SSH-Benutzer |
| `ansible_port` | SSH-Port |
| `backup_path` | Basisverzeichnis für Backups auf dem Control-Node |

`*.ini` ist in `.gitignore` eingetragen, `inventory.ini` wird daher nicht committet.

### Nutzung

```bash
ansible-playbook backup.yml -i inventory.ini
```

### Ablauf

1. RouterBOARD-Infos, Identity und System-Ressourcen auslesen
2. Dateiname und Zielpfad bilden
3. RAM-Disk `tmpBackup` erstellen (Fehler ignoriert, z. B. wenn bereits vorhanden)
4. Vorhandene Dateien in `tmpBackup` löschen (Fehler ignoriert)
5. `.backup` und `.rsc` in `tmpBackup` erstellen
6. Zielverzeichnis auf dem Control-Node erstellen
7. Beide Dateien per `scp` kopieren
8. Abbruch, falls eine der Dateien lokal fehlt
9. RAM-Disk `tmpBackup` entfernen (Fehler ignoriert)
10. Dateien auf dem Gerät auflisten (`/file print`); die Ausgabe wird nur registriert, nicht angezeigt

### Bekannte Einschränkungen

- Für alle SSH/SCP-Verbindungen wird `StrictHostKeyChecking=no` verwendet.
- Das Passwort wird per Kommandozeile übergeben (`sshpass -p`) und ist in der Prozessliste sichtbar; `no_log` ist nicht gesetzt.
- Die RAM-Disk ist auf 1 MB begrenzt (`tmpfs-max-size=1M`); grössere Backups schlagen fehl.

### Offene Aufgaben

- Bereinigung des Backup-Pfades anhand einer Aufbewahrungsdauer.
