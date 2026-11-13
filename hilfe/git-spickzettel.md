# Git-Spickzettel

[⬅️ Zurück zum Projektauftrag](../README.md)

## Die drei Bereiche von Git

```text
 Arbeitsverzeichnis  ──git add──►  Staging-Bereich  ──git commit──►  lokales Repository  ──git push──►  GitHub (origin)
   (deine Dateien)                 (vorgemerkt)                     (.git, Versionen)                   (Remote)
                                                                                      ◄──git pull───
```

## Einrichten (einmalig)

| Befehl | Wirkung |
|---|---|
| `git config --global user.name "Vorname K."` | Name für deine Commits |
| `git config --global user.email "…@users.noreply.github.com"` | E-Mail für deine Commits |
| `git config --global init.defaultBranch main` | neuer Standard-Branch heißt `main` |
| `git config --global pull.rebase false` | `git pull` führt bei Bedarf einen Merge durch |
| `git config --list` | Einstellungen anzeigen |

## Täglicher Ablauf

| Befehl | Wirkung |
|---|---|
| `git clone git@github.com:USER/REPO.git ~/ordner` | Repo von GitHub herunterladen |
| `git status` | Was ist geändert, vorgemerkt, unbekannt? **Im Zweifel immer zuerst!** |
| `git diff` | Zeilengenaue Änderungen anzeigen (noch nicht vorgemerkt) |
| `git add datei` / `git add ordner/` | Änderung für den nächsten Commit vormerken |
| `git commit -m "Was und warum"` | Vorgemerkte Änderungen als neue Version speichern |
| `git push` | Neue Commits zu GitHub hochladen |
| `git pull` | Neue Commits von GitHub holen und einarbeiten |
| `git log --oneline --graph --all` | Versionsgeschichte kompakt, mit allen Branches |
| `git restore datei` | Ungespeicherte Änderung an einer Datei verwerfen (**Vorsicht!**) |

## Branches

| Befehl | Wirkung |
|---|---|
| `git branch` | lokale Branches anzeigen (`*` = aktueller) |
| `git branch -a` | auch Branches auf GitHub (`remotes/origin/...`) anzeigen |
| `git switch -c dev` | neuen Branch `dev` anlegen **und** wechseln |
| `git switch main` | zu `main` wechseln |
| `git push -u origin dev` | neuen Branch zum ersten Mal hochladen (`-u` merkt sich das Ziel) |
| `git merge dev` | `dev` in den aktuellen Branch übernehmen (lokal, Alternative zum Pull Request) |
| `git branch -d dev` | gemergten Branch lokal löschen |

## `.gitignore`

Dateien und Ordner, die in `.gitignore` stehen, ignoriert Git: Sie tauchen in `git status` nicht auf und landen nicht versehentlich in einem Commit. Typisch sind Laufzeitdaten (`kuma-data/`), Logs, temporäre Dateien und **Geheimnisse** (Passwörter, Schlüssel).

```gitignore
kuma-data/      # ganzer Ordner
*.log           # alle Dateien mit Endung .log
.env            # Datei mit Passwörtern
```

> Eine Datei, die **schon** committet wurde, wird durch einen späteren Eintrag in `.gitignore` nicht wieder entfernt. Deshalb steht `.gitignore` von Anfang an im Repo.

## Gute Commit-Nachrichten

- Kurz (≤ 50 Zeichen), im Imperativ oder als Ergebnis: `Webserver auf Port 8085 umgestellt`
- Sagt **was** und möglichst **warum**, nicht „Update“, „fix“ oder „asdf“
- Ein Commit = eine zusammengehörige Änderung
