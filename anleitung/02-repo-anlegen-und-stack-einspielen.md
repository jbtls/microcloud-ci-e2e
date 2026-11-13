# 02 – Repository aus der Vorlage anlegen und den Stack einspielen

[⬅️ Zurück zum Projektauftrag](../README.md)

> **Ziel:** Dein Stack aus Modul 4 liegt in deinem eigenen GitHub-Repository und wird dort automatisch getestet.
> **Zeit:** ca. 45 Minuten

## Schritt 1: Eigenes Repository aus der Vorlage erzeugen (Browser)

1. Öffne die Vorlage: <https://github.com/jbtls/microcloud-ci>
2. Klicke oben rechts auf **Use this template** → **Create a new repository**.
3. Einstellungen:
   - *Owner:* dein Benutzername
   - *Repository name:* `microcloud`
   - Sichtbarkeit: **Public** ← wichtig, denn nur dann laufen die Tests garantiert kostenlos
   - *Include all branches:* **nicht** anhaken
4. **Create repository**.

Öffne in deinem neuen Repo den Tab **Actions**. Wahrscheinlich ist dort schon ein erster Lauf **rot** mit der Meldung *„Keine Compose-Datei gefunden“*. Das ist richtig so: Dein Stack fehlt ja noch.

> **Template statt Fork:** Dein Repo ist eine **Kopie mit eigener Geschichte**. Es hängt nicht an der Vorlage, und deine Pull Requests bleiben in deinem Repo.

## Schritt 2: Repository auf den LXC klonen

Kopiere auf GitHub über den grünen Button **Code** → Reiter **SSH** die Adresse, z. B. `git@github.com:benutzername/microcloud.git`.

Auf dem **LXC-Container**:

```bash
# 1. Alten Stack aus Modul 4 stoppen (gibt Port 8080 frei)
cd ~/microcloud
sudo docker compose down

# 2. Alten Ordner umbenennen – er bleibt als Sicherung erhalten
cd ~
mv ~/microcloud ~/microcloud-alt

# 3. Dein neues Repo genau an die alte Stelle klonen
git clone git@github.com:benutzername/microcloud.git ~/microcloud
cd ~/microcloud
ls -la
```

**Checkpoint:** Du siehst `README.md`, `.gitignore`, die Ordner `.github`, `anleitung`, `hilfe`, `kanban` und den versteckten Ordner `.git`. In `.git` liegt die komplette Versionsgeschichte.

## Schritt 3: Deinen Stack übernehmen

```bash
cp ~/microcloud-alt/compose.yml ~/microcloud/
cp -r ~/microcloud-alt/html ~/microcloud/
```

*Nur falls du in Modul 4 den Bonus mit Uptime Kuma gemacht hast und deine Monitore behalten willst:*

```bash
sudo cp -a ~/microcloud-alt/kuma-data ~/microcloud/
```

## Schritt 4: Was sieht Git?

```bash
git status
```

Git listet `compose.yml` und `html/` als **Untracked files** auf, also neue Dateien, die Git noch nicht verfolgt. Ein eventuell kopierter Ordner `kuma-data/` taucht **nicht** auf. Schau in die Datei `.gitignore`: Dort steht er drin. Die Datenbank von Uptime Kuma entsteht erst auf dem Server zur Laufzeit und gehört nicht ins Repository.

## Schritt 5: Erster Commit und Push

```bash
git add compose.yml html/
git status                  # jetzt grün: "Changes to be committed"
git commit -m "Microcloud-Stack aus Modul 4 übernommen"
git push
git log --oneline
```

## Schritt 6: Den CI-Lauf beobachten

1. Öffne auf GitHub den Tab **Actions**. Oben steht ein Lauf mit deiner Commit-Nachricht.
2. Klicke ihn an, dann den Job **Compose-Stack testen (Port 8085)**.
3. Warte, bis er fertig ist. Er wird **rot**. 😱 Keine Sorge, das ist Absicht.

Wie du ein CI-Log liest: [Hilfe – CI-Log lesen](../hilfe/ci-log-lesen.md)

## Aufgaben

1. In **welchem Schritt** (Nummer und Name) schlägt der Lauf fehl?
2. Notiere die **Fehlermeldung** wörtlich.
3. Erkläre mit eigenen Worten, **warum** der Test fehlschlägt, obwohl dein Stack in Modul 4 doch funktioniert hat.
4. Welche Schritte wurden danach **übersprungen**, welche trotzdem ausgeführt? Woran liegt das?

<details>
<summary>Lösung anzeigen</summary>

1. Schritt **4 · Port 8085 prüfen**.
2. `Kein Service in deiner compose.yml veröffentlicht den Host-Port 8085. Aktuell veröffentlicht: web → 8080:80` (bei Kuma-Bonus zusätzlich `kuma → 3001:3001`).
3. Der Stack ist technisch in Ordnung, erfüllt aber die **neue Anforderung** der Firma nicht: Der Webserver muss auf dem Host-Port 8085 erreichbar sein, er veröffentlicht aber 8080. Ein automatischer Test prüft genau solche vereinbarten Anforderungen.
4. Die Schritte 5–7 wurden übersprungen: Ein Job bricht nach dem ersten fehlgeschlagenen Schritt ab. Die Schritte 8 (Steckbrief), 9 (Logs) und 10 (Aufräumen) laufen trotzdem, weil sie in der Workflow-Datei mit `if: always()` bzw. `if: failure()` markiert sind.

</details>

---

[⬅️ 01 – SSH-Schlüssel](01-ssh-key.md) · [➡️ Weiter: 03 – Port anpassen, erster grüner Build](03-port-anpassen-erster-gruener-build.md)
