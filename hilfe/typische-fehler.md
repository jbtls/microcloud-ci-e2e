# Typische Fehler und Lösungen

[⬅️ Zurück zum Projektauftrag](../README.md)

## Git und GitHub

| Meldung / Symptom | Ursache | Lösung |
|---|---|---|
| `git@github.com: Permission denied (publickey).` | GitHub kennt deinen öffentlichen Schlüssel nicht, oder du hast den falschen hochgeladen. | `cat ~/.ssh/id_ed25519.pub` erneut bei GitHub → *SSH and GPG keys* eintragen. Mit `ssh -T git@github.com` testen. |
| `ssh: connect to host github.com port 22: Connection timed out` | Port 22 ist durch eine Firewall gesperrt. | SSH über Port 443, siehe [01 – SSH-Schlüssel](../anleitung/01-ssh-key.md#falls-port-22-gesperrt-ist). |
| `Please tell me who you are` beim Commit | `user.name` / `user.email` fehlen. | `git config --global user.name …` und `user.email …` setzen. |
| `fatal: not a git repository` | Du bist nicht im Repo-Ordner. | `cd ~/microcloud` |
| `remote: Support for password authentication was removed` | Du hast mit `https://…` geklont, GitHub akzeptiert dort kein Passwort. | Auf SSH umstellen: `git remote set-url origin git@github.com:USER/microcloud.git` |
| `! [rejected] main -> main (fetch first)` | Auf GitHub gibt es Commits, die du lokal nicht hast (z. B. nach einem Merge im Browser). | Erst `git pull`, dann `git push`. |
| `There is no tracking information for the current branch` / `has no upstream branch` | Neuer Branch, der noch nie gepusht wurde. | `git push -u origin dev` |
| Editor (vim) öffnet sich bei `git pull` oder `git commit` | Git möchte eine Nachricht (z. B. für einen Merge-Commit). | Nachricht übernehmen und speichern: in vim `Esc`, dann `:wq` + `Enter`. |
| `kuma-data/` taucht in `git status` auf | `.gitignore` fehlt oder wurde verändert. | `.gitignore` prüfen, dort muss `kuma-data/` stehen. |
| Pull Request zeigt *„There isn't anything to compare“* | `dev` enthält keine neuen Commits gegenüber `main`, oder die Richtung ist vertauscht. | `base: main` ← `compare: dev` prüfen. Wurde auf `dev` gepusht? |

## Docker und Compose

| Meldung / Symptom | Ursache | Lösung |
|---|---|---|
| `Bind for 0.0.0.0:8085 failed: port is already allocated` | Ein anderer Container belegt den Port, oft der alte Stack. | `sudo docker ps` → alten Container stoppen (`sudo docker rm -f NAME`) bzw. im alten Ordner `sudo docker compose down` |
| `Conflict. The container name "/technova-web" is already in use` | Ein Container mit demselben `container_name` existiert noch (z. B. aus `~/microcloud-alt`). | `sudo docker rm -f technova-web`, dann erneut `sudo docker compose up -d` |
| `yaml: line X: found character that cannot start any token` | Tabulator in der YAML-Datei | Nur Leerzeichen verwenden (2 pro Ebene). |
| `curl` liefert *403 Forbidden* | Im Web-Ordner des Containers liegt keine `index.html`. | Volume-Pfad prüfen (nginx: `/usr/share/nginx/html`, Apache: Doku des `httpd`-Images). |
| Apache zeigt *„It works!“* | Volume hängt am falschen Ordner, Apache zeigt seine Standardseite. | siehe [05 – dev-Branch](../anleitung/05-dev-branch-apache.md#schritt-4-recherche--wo-erwartet-apache-die-webseiten) |

## Die CI

| Fehlgeschlagener Step | Bedeutung |
|---|---|
| 2 · Compose-Datei suchen | Keine `compose.yml` im Hauptordner des Repos. Committet und gepusht? |
| 3 · YAML-Syntax prüfen | Die Datei ist kein gültiges Compose-YAML (Einrückung, Tabs, Doppelpunkte). |
| 4 · Port 8085 prüfen | Kein Dienst veröffentlicht den **Host**-Port 8085. |
| 6 · Erreichbarkeit | Der Container läuft, aber hinter Port 8085 antwortet niemand. Stimmt der **Container**-Port? |
| 7 · Inhalt prüfen | Der Server antwortet, liefert aber nicht deine `html/index.html`. Stimmt der Volume-Pfad? |

Mehr dazu: [CI-Log lesen](ci-log-lesen.md)
