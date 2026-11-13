# 05 – Entwicklungszweig `dev`: Umstieg von nginx auf Apache

[⬅️ Zurück zum Projektauftrag](../README.md)

> **Ziel:** Auf dem Branch `dev` läuft Apache httpd, liefert deine Seite aus und die CI ist grün. `main` bleibt währenddessen unverändert.
> **Zeit:** ca. 90 Minuten

## Warum ein eigener Branch?

`main` ist der **Betriebsstand**: Was dort liegt, muss funktionieren. Eine größere Umstellung wie der Wechsel des Webservers wird deshalb auf einem eigenen **Branch** (Zweig) vorbereitet und getestet. Erst wenn alles grün ist, wird sie nach `main` übernommen (Phase 6).

```text
main  ●───●───●─────────────────────────●   ← Betriebsstand (nginx), bleibt stabil
               \                        /
dev             ●──── ✗ ────── ✓ ──────     ← Umbau auf Apache, darf zwischendurch rot sein
```

## Schritt 1: Branch anlegen und wechseln

```bash
cd ~/microcloud
git switch -c dev
git branch                 # Stern (*) zeigt: du bist auf dev
```

## Schritt 2: Der naheliegende erste Versuch

Ändere in der `compose.yml` **nur das Image** des Dienstes `web`:

```yaml
    image: httpd:2.4-alpine
```

Committe und pushe. Beim ersten Push eines neuen Branches musst du Git sagen, wohin er gehört (`-u` = *upstream*):

```bash
git add compose.yml
git commit -m "Webserver auf Apache httpd umgestellt"
git push -u origin dev
```

## Schritt 3: Rot – warum?

Öffne den Tab **Actions**. Der Lauf für `dev` wird **rot**.

1. In welchem Schritt scheitert er? Notiere die Fehlermeldung.
2. Schau dir die `diff`-Ausgabe im Log an. Was liefert Apache stattdessen aus?
3. Teste lokal und vergleiche:

   ```bash
   sudo docker compose up -d
   curl http://localhost:8085
   ```

> **Merke:** Port 8085 antwortet, der Container läuft, ein Test „läuft der Server?“ wäre grün. Erst die Inhaltsprüfung zeigt, dass der Dienst **nicht das Richtige** tut.

## Schritt 4: Recherche – wo erwartet Apache die Webseiten?

Jedes Webserver-Image hat einen eigenen **Web-Ordner** (*Document Root*) im Container. Bei nginx war es `/usr/share/nginx/html`. Finde heraus, welcher Ordner es beim offiziellen Image `httpd` ist.

- Quelle: die Beschreibung des Images auf Docker Hub: <https://hub.docker.com/_/httpd>, Abschnitt *How to use this image*
- Alternative ohne Browser: Schau direkt in den Container:

  ```bash
  sudo docker compose exec web sh -c 'grep -i "^DocumentRoot" /usr/local/apache2/conf/httpd.conf'
  ```

<details>
<summary>Hinweis, falls du nicht weiterkommst</summary>

Der Document Root des offiziellen `httpd`-Images ist `/usr/local/apache2/htdocs/`. Dein Volume muss also `./html` dorthin einbinden, weiterhin schreibgeschützt (`:ro`).

</details>

## Schritt 5: Korrigieren, lokal testen, pushen

1. Passe die Zeile unter `volumes:` beim Dienst `web` an.
2. Lokal testen:

   ```bash
   sudo docker compose up -d
   curl http://localhost:8085        # jetzt wieder deine Statusseite?
   curl -I http://localhost:8085     # Header: Server: Apache/2.4...
   ```

3. Committen und pushen (der Branch kennt seinen *upstream* jetzt, `git push` reicht):

   ```bash
   git add compose.yml
   git commit -m "Volume auf Apache-Document-Root angepasst"
   git push
   ```

4. Tab **Actions**: Der Lauf auf `dev` ist **grün**. Im **Steckbrief** (Summary) steht jetzt `Apache/2.4.x (Unix)` statt `nginx/...`.

## Schritt 6: Zwei Welten vergleichen

```bash
git switch main
grep image: compose.yml     # main: immer noch nginx
git switch dev
grep image: compose.yml     # dev: Apache
git log --oneline --graph --all
```

## Aufgaben

1. Erkläre, warum der erste Versuch auf `dev` rot war, obwohl der Container lief und Port 8085 antwortete.
2. Wie lautet deine endgültige Konfiguration des Dienstes `web`?
3. Warum hat der rote Lauf auf `dev` den Betrieb **nicht** gestört?
4. Der Container-Port blieb `80`. Woher weißt du, dass auch Apache im Container auf Port 80 lauscht?

<details>
<summary>Lösung anzeigen</summary>

1. Das Volume `./html` war noch auf `/usr/share/nginx/html` eingebunden. Diesen Ordner benutzt Apache nicht. Apache liefert deshalb seine eingebaute Standardseite (`<html><body><h1>It works!</h1></body></html>`) aus. Schritt **7 · Inhalt prüfen** findet den Unterschied zu `html/index.html`.
2. Zum Beispiel:

   ```yaml
     web:
       image: httpd:2.4-alpine
       container_name: technova-web
       restart: unless-stopped
       ports:
         - "8085:80"
       volumes:
         - ./html:/usr/local/apache2/htdocs:ro
   ```

3. Die Änderung lag nur auf `dev`. `main`, der Betriebsstand, war davon unberührt und blieb grün. Genau dafür gibt es Branches.
4. Aus der Doku des Images (`EXPOSE 80` bzw. `Listen 80` in der `httpd.conf`). Praktisch hat es auch die CI bewiesen: Schritt 6 war grün.

</details>

---

[⬅️ 04 – Workflow verstehen](04-workflow-verstehen.md) · [➡️ Weiter: 06 – Pull Request & Merge](06-pull-request-und-merge.md)
