# 06 – Pull Request: erst grün, dann `main`

[⬅️ Zurück zum Projektauftrag](../README.md)

> **Ziel:** Die Apache-Umstellung ist über einen Pull Request nach `main` übernommen, **nachdem** der Test grün war, und dein LXC betreibt den neuen Stand.
> **Zeit:** ca. 45 Minuten

## Was ist ein Pull Request?

Ein **Pull Request** (PR) ist ein Antrag: *„Bitte übernimm die Änderungen aus `dev` in `main`.“* Auf der PR-Seite sieht man alle Änderungen, kann sie kommentieren, und die CI testet automatisch, **wie `main` nach dem Merge aussehen würde**. In Teams prüft hier oft noch eine zweite Person die Änderungen (Code-Review), bevor gemergt wird.

Die Regel bei TechNova lautet: **Gemergt wird nur, wenn alle Checks grün sind.**

## Schritt 1: Pull Request erstellen (Browser)

1. Öffne dein Repo auf GitHub. Meist erscheint oben ein gelber Hinweis *„dev had recent pushes“* → **Compare & pull request**.
   Alternativ: Tab **Pull requests** → **New pull request**.
2. Prüfe die Richtung: **base: `main`** ← **compare: `dev`**.
3. Titel z. B. `Umstellung Webserver auf Apache httpd`. In die Beschreibung schreibst du in 2–3 Sätzen, **was** du geändert hast und **warum**.
4. **Create pull request**.

## Schritt 2: Checks abwarten

Unten auf der PR-Seite erscheint der Bereich **Checks**. Dort läuft der Test *Compose-Stack testen (Port 8085)* erneut, diesmal für den Pull Request.

- ⏳ gelb = läuft noch → warten
- ❌ rot = **nicht mergen!** Erst auf `dev` korrigieren und pushen. Der PR aktualisiert sich automatisch.
- ✅ grün = bereit zum Merge

Schau dir auch den Reiter **Files changed** an: Dort siehst du genau die Zeilen, die sich zwischen `main` und `dev` unterscheiden.

## Schritt 3: Mergen

Erst wenn alle Checks grün sind: **Merge pull request** → **Confirm merge**.

Danach startet die CI automatisch noch einmal, jetzt für `main`. Prüfe im Tab **Actions**, dass auch dieser Lauf grün ist, und ob das Abzeichen im README auf **passing** steht.

*Optional:* GitHub bietet nach dem Merge **Delete branch** an. Der Branch `dev` wird nicht mehr gebraucht, die Commits bleiben in `main` erhalten.

## Schritt 4: Den LXC auf den Stand von `main` bringen

```bash
cd ~/microcloud
git switch main
git pull                          # holt den Merge von GitHub
grep image: compose.yml           # jetzt: httpd:2.4-alpine
sudo docker compose up -d         # Compose erkennt die Änderung und erstellt den Container neu
curl -I http://localhost:8085     # Server: Apache/2.4...
git log --oneline --graph
```

> Das ist der Arbeitsablauf der Woche im Kleinen: **Änderung auf einem Branch → automatisch getestet → nach grünem Test in `main` → von `main` auf den Server.**

## Reflexion

1. Beschreibe den Weg einer Änderung von deinem Editor bis in den Betrieb, mit allen Stationen dieser Woche.
2. Was hätte passieren können, wenn du den ersten (roten) Apache-Versuch direkt auf `main` gepusht und auf dem LXC ausgerollt hättest?
3. In deinem Repo hält dich technisch nichts davon ab, einen roten PR zu mergen. Was sorgt in einem Team dafür, dass die Regel „nur grün mergen“ trotzdem eingehalten wird?
4. Welche Vorteile hat es, dass deine komplette Konfiguration jetzt auf GitHub liegt? Denke an einen Totalausfall deines LXC-Containers.

<details>
<summary>Lösungshinweise</summary>

1. `compose.yml` ändern → lokal testen → `git add`/`git commit` → `git push` auf `dev` → CI testet automatisch → Pull Request nach `main` → CI testet den PR → Merge nach grünem Test → CI auf `main` → auf dem Server `git pull` + `docker compose up -d`.
2. Die Statusseite wäre im Betrieb durch die Apache-Standardseite „It works!“ ersetzt worden. Der Dienst hätte geantwortet, aber den falschen Inhalt geliefert. Eine Überwachung, die nur den Port prüft, hätte den Fehler nicht einmal bemerkt.
3. Absprachen und Teamdisziplin, Code-Reviews durch eine zweite Person und die gut sichtbaren Checks am PR. Größere Teams lassen GitHub die Regel zusätzlich technisch durchsetzen. Hier gilt sie als Konvention.
4. Die Konfiguration ist gesichert und versioniert. Jede Änderung ist mit Autor, Zeitpunkt und Begründung nachvollziehbar, alte Stände sind wiederherstellbar. Nach einem Ausfall reichen ein neuer Container, `git clone` und `docker compose up -d`, und der Dienst läuft wieder (Infrastructure as Code).

</details>

---

[⬅️ 05 – dev-Branch](05-dev-branch-apache.md) · [➡️ Weiter: 07 – Zusatz](07-zusatz-cicd-test.md)
