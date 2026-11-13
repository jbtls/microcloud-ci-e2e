# 03 – Port anpassen: dein erster grüner Build

[⬅️ Zurück zum Projektauftrag](../README.md)

> **Ziel:** Dein Stack veröffentlicht Port 8085, läuft lokal und der automatische Test wird grün.
> **Zeit:** ca. 30 Minuten

## Der Arbeitsablauf, den du ab jetzt immer nutzt

```text
  ändern  ──►  lokal testen  ──►  git add / commit  ──►  git push  ──►  CI prüft  ──►  ✅ / ❌
    ▲                                                                                  │
    └──────────────────────────────── bei ❌: Log lesen, korrigieren ◄─────────────────┘
```

## Schritt 1: Port ändern

```bash
cd ~/microcloud
nano compose.yml
```

Ändere beim Dienst `web` den **Host-Port** (links vom Doppelpunkt) von `8080` auf `8085`. Der Container-Port rechts bleibt `80`, denn darauf lauscht nginx im Container:

```yaml
    ports:
      - "8085:80"
```

## Schritt 2: Lokal testen, bevor du pushst

```bash
sudo docker compose up -d
sudo docker compose ps
curl http://localhost:8085
```

**Checkpoint:** `docker compose ps` zeigt `0.0.0.0:8085->80/tcp`, und `curl` gibt den HTML-Code deiner Statusseite aus.

*Im Browser deines Schul-PCs:* SSH-Tunnel wie in Modul 4, aber mit dem neuen Port:

```bash
ssh root@<Node-IP> -p <20xxx> -L 8085:localhost:8085
```

Dann im Browser `http://localhost:8085` öffnen.

> Passe auch den Hinweis in deiner `html/index.html` an (`172.31.X.Y:8085`), damit deine Seite stimmt. Der Test vergleicht die ausgelieferte Seite mit der Datei im Repo. Beides kommt aus derselben Datei, also passt es automatisch.

## Schritt 3: Änderung ansehen, committen, pushen

```bash
git status
git diff                   # zeigt genau, welche Zeilen sich geändert haben
git add compose.yml html/index.html
git commit -m "Webserver auf Port 8085 umgestellt"
git push
```

## Schritt 4: Grün!

Öffne auf GitHub den Tab **Actions** und beobachte den neuen Lauf. Wenn alles stimmt, bekommt er einen **grünen Haken** ✅.

Öffne außerdem die Startseite deines Repos: Das Abzeichen ganz oben im README zeigt jetzt **passing**.

Klicke im grünen Lauf auf **Summary**. Unten steht der **Steckbrief** deines Stacks. Notiere, welcher Webserver im Feld *Server* angegeben ist.

## Aufgaben

1. Warum testest du lokal, bevor du pushst, obwohl die CI ohnehin testet?
2. Was zeigt `git log --oneline` jetzt an? Wie viele Commits hat dein Repo, und wer hat sie erstellt?
3. Was würde passieren, wenn du in der `compose.yml` versehentlich `"8085:8085"` schreibst? Überlege zuerst und prüfe dann, an welchem Schritt die CI scheitern würde.

<details>
<summary>Lösung anzeigen</summary>

1. Lokales Testen gibt **sofort** Rückmeldung und hält die Historie sauber, ohne „Fix“-Commits. Die CI ist das **Sicherheitsnetz**: Sie prüft in einer sauberen, immer gleichen Umgebung und findet auch, was „nur bei mir“ funktioniert.
2. Mindestens drei Commits: den Initial-Commit aus der Vorlage (erstellt von GitHub bzw. der Lehrkraft), „Microcloud-Stack aus Modul 4 übernommen“ und „Webserver auf Port 8085 umgestellt“ (beide von dir, mit dem Namen aus `git config`).
3. Schritt 4 wäre grün, denn Host-Port 8085 wird veröffentlicht. **Schritt 6** scheitert aber: Docker leitet Port 8085 auf den Container-Port 8085 weiter, nginx lauscht im Container jedoch auf Port 80. Es antwortet also niemand.

</details>

---

[⬅️ 02 – Repo anlegen](02-repo-anlegen-und-stack-einspielen.md) · [➡️ Weiter: 04 – Den Workflow verstehen](04-workflow-verstehen.md)
