# 04 – Den Workflow verstehen

[⬅️ Zurück zum Projektauftrag](../README.md)

> **Ziel:** Du kannst erklären, was die CI in jedem Schritt prüft, und die Fachbegriffe von CI/CD zuordnen.
> **Zeit:** ca. 45 Minuten · **Material:** [`.github/workflows/microcloud-ci.yml`](../.github/workflows/microcloud-ci.yml), [Begriffe CI/CD](../hilfe/begriffe-cicd.md)

Beantworte die Fragen im Heft oder in einer neuen Datei `antworten.md` in deinem Repo. Wenn du die Datei nimmst: committen und pushen nicht vergessen, das ist gleich noch eine Übung.

## Teil A: Aufbau der Workflow-Datei

Öffne die Datei [`.github/workflows/microcloud-ci.yml`](../.github/workflows/microcloud-ci.yml) auf GitHub oder mit `less .github/workflows/microcloud-ci.yml` auf dem LXC.

1. Bei welchen **Ereignissen** (Events) startet der Workflow? Nenne alle drei.
2. Auf welchem **Runner** (Betriebssystem) läuft der Job? Wer stellt diesen Rechner bereit?
3. Wie heißt der **Job**, und wie viele **Steps** hat er?
4. Welcher Step nutzt eine fertige **Action** (`uses:`), welche führen eigene Befehle aus (`run:`)?
5. Welche Rechte hat der Workflow (`permissions`)? Warum ist es sinnvoll, sie so knapp zu halten?
6. Der Job hat die Bedingung `if: ${{ !github.event.repository.is_template }}`. Was bewirkt sie in der Vorlage und was in deinem Repo?

## Teil B: Was wird geprüft?

7. Ordne zu: Welcher Step würde **zuerst** fehlschlagen, wenn …
   1. … in der `compose.yml` ein Tabulator statt Leerzeichen steht?
   2. … der Ordner `html/` nie committet wurde?
   3. … `ports: - "8085:8080"` eingetragen ist?
   4. … die `compose.yml` versehentlich in einem Unterordner liegt?
8. Step 7 vergleicht mit `diff`, ob der Server **genau deine** `html/index.html` ausliefert. Warum reicht es nicht, nur zu prüfen, ob Port 8085 antwortet? Denke an Phase 5 …
9. Die Steps 8–10 haben `if: always()` bzw. `if: failure()`. Wozu dient Step 10 *Aufräumen*, obwohl der Runner nach dem Lauf ohnehin gelöscht wird?

## Teil C: Einordnen

10. Ist das, was dieser Workflow tut, **Continuous Integration**, **Continuous Delivery** oder **Continuous Deployment**? Begründe.
11. Nenne zwei Vorteile, die die TechNova GmbH durch diesen automatischen Test hat, im Vergleich dazu, dass jeder Admin „mal kurz mit `curl` testet“.

<details>
<summary>Lösung anzeigen</summary>

1. `push` (auf jeden Branch), `pull_request` (Ziel-Branch `main`) und `workflow_dispatch` (manuell per Knopf im Actions-Tab).
2. `ubuntu-latest`, eine frische virtuelle Ubuntu-Maschine (*GitHub-hosted runner*), die GitHub für jeden Lauf neu startet und danach verwirft. Docker und Docker Compose sind vorinstalliert.
3. Job `stack-test` (Anzeigename „Compose-Stack testen (Port 8085)“) mit 10 Steps.
4. Nur Step 1 nutzt eine Action (`actions/checkout@v7`, lädt den Code des Repos auf den Runner). Alle anderen führen Shell-Befehle mit `run:` aus.
5. `contents: read`: Der Workflow darf den Code nur lesen, nicht verändern und nichts veröffentlichen. Das ist das **Prinzip der minimalen Rechte**: Falls ein Schritt kompromittiert wird, kann er wenig Schaden anrichten.
6. In der Vorlage (ein *Template-Repository*) gibt es noch keinen Stack. Der Job wird dort **übersprungen**, damit die Vorlage nicht dauerhaft rot ist. In deinem Repo (kein Template) läuft er immer.
7. (a) Step 3 *YAML-Syntax prüfen*, (b) Step 7 *Inhalt prüfen*, denn `html/index.html` fehlt, (c) Step 6 *Erreichbarkeit*, denn Docker leitet auf Container-Port 8080 weiter, der Webserver lauscht aber auf 80, (d) Step 2 *Compose-Datei suchen*.
8. Ein Webserver antwortet auch dann auf Port 8085, wenn er **die falsche Seite** ausliefert, z. B. seine eingebaute Standardseite, weil das Volume an der falschen Stelle hängt. Ein guter Test prüft das **Ergebnis, das der Kunde erwartet**, nicht nur, ob „irgendwas läuft“.
9. Step 10 sorgt für ein sauberes Ende (Container und Netzwerke werden entfernt, Logs abgeschlossen). Auf **selbst gehosteten** Runnern, die wiederverwendet werden, würden sonst Container liegen bleiben und Ports blockieren. Aufräumen gehört zu jedem sauberen Test.
10. **Continuous Integration**: Jede Änderung wird automatisch gebaut und getestet. Delivery (Bereitstellen eines fertigen Artefakts, z. B. eines Images in einer Registry) und Deployment (automatisches Ausrollen auf den Server) passieren hier nicht. Den LXC aktualisierst du selbst mit `git pull`.
11. Zum Beispiel: Der Test läuft **bei jeder Änderung** und wird nicht vergessen. Er läuft **immer gleich** in einer sauberen Umgebung („bei mir geht's“ zählt nicht). Fehler fallen **vor** dem Betrieb auf, das Ergebnis ist **dokumentiert und für alle sichtbar** (Haken am Commit, Abzeichen).

</details>

---

[⬅️ 03 – Port anpassen](03-port-anpassen-erster-gruener-build.md) · [➡️ Weiter: 05 – dev-Branch: Umstieg auf Apache](05-dev-branch-apache.md)
