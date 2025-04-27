---
title: Wie du Linux unter Windows (WSL) RICHTIG verwendest!
date: 2025-04-27
draft: true
tags:
  - WSL
  - Linux
  - Windows
  - Ubuntu
  - Kali
---
# Windows Subsystem Linux
Wir werden uns heute anschauen, wie du Linux (Ubuntu, Kali, ...) unter Windows ganz simpel verwenden kannst, und welche Möglichkeiten dir dies bietet.
![[Pasted image 20250427070055.png]]

Um WSL2 auf Windows zu verwenden, ist folgendes möglich
- Hardware 64-bit
- min. 4GB Ram
- Virtualization muss aktiviert sein
- Windows 11

--> All dies sollte bei den meisten Leuten bereits passen

Aber warum sollte man Linux unter Windows benutzen...?

## Warum WSL?
Deine Welt der Möglichkeiten expandiert wie das Universum. Fertig.

Nein Spaß, hier sind einige Gründe, weshalb du WSL verwenden solltest.

1. Warum nicht. WSL ist in Windows direkt eingebaut und _kostet nichts extra_
2. Du hast eine Linux-Umgebung direkt unter Windows und kannst echte Linux-Tools (`bash`, `ssh`,  `git`, ...) nativ unter Windows benutzen
3. Sehr leichtgewichtig
	- Im Vergleich zu einer kompletten VM, verbraucht WSL deutlich weniger RAM und CPU-Leistung
4. Gemeinsamer Zugriff auf Dateien
	- Dein Windos-Dateisystem (`C:\`, `D:\` etc.) ist direkt unter WSL erreichbar und umgekehrt

Außderdem ist es ein sehr spannendes Thema, falls man mit `Docker` arbeitet, aber dazu nächstes mal mehr.
Ich könnte noch weiter machen, doch dieser Beitrag, soll sich ja um das wie und nicht um das was und warum drehen.
Also springen wir direkt in die Praxis😁.
## Installation
- Installiere WSL auf Windows
```shell
wsl --install # Admin Rechte benötigt
```

- Installiere eine Ubuntu Distribution
```shell
wsl -d ubuntu
```

- Man kann auch andere Distributionen installieren
	- Folgender Befehl gibt alle verfügbaren Distributionen aus
``` shell
wsl --list --online # zeigt alle verfügbaren Distros

wsl -d kali-linux # installiere beispielsweise kali Distribution
```
- Es ist ebenso möglich WSL Distributionen über den Microsoft Store herunterzuladen
	- Ja, das geht wirklich

![[Pasted image 20250427063914.png]]


```shell
wsl -d ubuntu # name von der distro zum öffnen der distro
```

---
## Windows Befehle in WSL
- Ja, im WSL Terminal kann man auch Windows Befehle verwenden, wenn man `.exe` hinter den Programmnamen anhängt

``` shell
# inside wsl (ubuntu)
ipconfig.exe 
ping.exe
notepad.exe
explorer.exe
.
.
.

```

- auch kann man Linux Befehle im Windows Terminal wie folgt verwenden
	- dazu muss man lediglich `wsl` vor den Befehl schreiben 
```shell
ipconfig | wsl grep 10. # zum filtern

wsl cat
```

Ist das nicht geil
Die Betriebssysteme arbeiten quasi Hand in Hand
![[Pasted image 20250427064653.png]]

---
# Exkursion: spiderfoot

OSINT tool (open source intelligence tool) for hacker
- `spiderfoot` ist eine OSINT (Open Source for Intelligence Tool)
- Man kann das Programm wie folgt in `kali-linux` starten

```shell
spiderfoot -l 127.0.0.1:5001 # <ip>:<port>
```
- Dann gibt man den Socket (in unserem Fall 127.0.0.1:5001) im Browser ein und kann das Tool verwenden
---

## Installierte Distros ausgeben 
```shell
wsl --list
wsl --list --verbose # für mehr Infos
```
- Default Distor wird mit \* markiert

## Default ändern
- Hier wird der Default beispielsweise auf `kali-linux` gesetzt
```shell
wsl --set-default kali-linux 
```

## WSL ,,ausschalten"
```shell
wsl --shutdown # stoppt alle distros
wsl --terminate kali-linux # um nur kali-linux zu stoppen
```

- Weitere Informationen erhalten
```
wsl status
```

## Löschen von Distros
```shell
wsl --unregister kali-linux # zum löschen von kali-linux
```

## Auf Windows Dateien zugreifen
- Man kann in WSL über `/mnt/c` auf den Windows Harddrive zugreifen
``` shell
cd /mnt/c # windows harddrive
```

## Auf Linux Dateien zugreifen
- Man kann in einem Linux Ordner (bsp. `/home/user`) `explorer.exe .` aufrufen
	- Öffnet Windows Datei Explorer und zeigt auf den Linux Ordner
```shell
explorer.exe . # in wsl home verzeichnes eingeben
```

---
# kali-win-kex
- Ein weiteres cooles Tool für `kali` ist `kali-win-kex`
	- Damit kann man sich eine Kali-Umgebung erzeugen
```shell
sudo apt install kali-win-kex -y # installiere kali-win-kex
kex # erzeuge Umgebung
```
- F8 für Einstellungen
- `kex --win --stop` zum stoppen oder einfach ausloggen
![[Pasted image 20250427065644.png]]
---

## WSL Plugin für VSCode
- Ein Must-Have ist ebenso das WSL Plugin für VSCode
	- erlaubt das arbeiten mit Dateien in WSL
	- WSL Terminal in VSCode
	- und weitere Vorteile