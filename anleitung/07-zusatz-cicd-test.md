# 07 – Zusatz: CI/CD weitergedacht

[⬅️ Zurück zum Projektauftrag](../README.md)

> Für alle, deren Projekt die *Definition of Done* erfüllt. Die Aufgaben sind unabhängig voneinander.

## A) Wiki-Projekt „cicd-test“: vom Test zum Release

Bisher hat deine CI nur **getestet** (Continuous Integration). Im Projekt **cicd-test** geht die Pipeline weiter: Sie baut aus einem `Dockerfile` ein **eigenes Image**, testet es auf einem `dev`-Branch und veröffentlicht es bei einem Versions-Tag (z. B. `v1.0.0`) in der **GitHub Container Registry** (Continuous Delivery).

**Start:** <https://github.com/jbtls/cicd-test/wiki>. Arbeite die Seiten und Aufgaben dort der Reihe nach durch.

Hinweise, die dir dort helfen:

- **Dort wird geforkt, nicht aus einer Vorlage erzeugt.** Lass den Fork **öffentlich** (public), dann sind Actions und Packages kostenlos.
- **Actions im Fork freischalten:** In einem frisch geforkten Repo sind Workflows aus Sicherheitsgründen zunächst **deaktiviert**. Öffne im Fork den Tab **Actions** und klicke auf **I understand my workflows, go ahead and enable them**. Sonst passiert bei deinen Pushes nichts.
- **Pull Requests im Fork:** Erstellst du einen Pull Request, schlägt GitHub als Ziel standardmäßig das **Original-Repo** (`jbtls/cicd-test`) vor. Stelle bei *base repository* **deinen Fork** ein.
- Du kannst den Fork direkt auf deinem LXC klonen und mit deinem SSH-Schlüssel arbeiten. Dort ist Docker schon installiert, praktisch für Aufgabe 6 (Image aus der Registry ausführen).

## B) Eigener Test für Uptime Kuma

Falls dein Stack den `kuma`-Dienst aus Modul 4 enthält:

1. Lege einen Branch `kuma-test` an.
2. Erweitere [`.github/workflows/microcloud-ci.yml`](../.github/workflows/microcloud-ci.yml) um Schritte, die zusätzlich den Dienst `kuma` starten und prüfen, ob Port 3001 innerhalb von 60 Sekunden antwortet. Orientiere dich an Step 5 und Step 6.
3. Push → CI grün? → Pull Request → Merge.

> Pushst du Änderungen an Workflow-Dateien per SSH, funktioniert alles wie gewohnt. Über HTTPS mit einem Token bräuchte der Token zusätzlich die Berechtigung **workflow**.

## C) Ein Release erstellen

```bash
git switch main && git pull
git tag v1.0.0
git push origin v1.0.0
```

Auf GitHub: **Releases** → **Draft a new release** → Tag `v1.0.0` wählen → Titel und kurze Beschreibung deiner Änderungen („Was ist neu?“) → **Publish release**.

Frage: Was ist der Unterschied zwischen einem Commit, einem Branch und einem Tag?

<details>
<summary>Lösung anzeigen</summary>

Ein **Commit** ist ein gespeicherter Stand mit eindeutiger ID. Ein **Branch** ist ein beweglicher Zeiger, der mit jedem neuen Commit weiterwandert. Ein **Tag** ist ein fester Zeiger auf genau einen Commit, z. B. eine Versionsnummer, und bewegt sich nie.

</details>

---

[⬅️ 06 – Pull Request & Merge](06-pull-request-und-merge.md) · [Projektauftrag](../README.md)
