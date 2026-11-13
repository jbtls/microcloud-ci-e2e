# Begriffe: CI/CD und GitHub Actions

[⬅️ Zurück zum Projektauftrag](../README.md)

## CI, CD und CD

| Begriff | Bedeutung | In diesem Projekt |
|---|---|---|
| **Continuous Integration (CI)** | Änderungen werden **regelmäßig** in ein gemeinsames Repository integriert und dabei **automatisch gebaut und getestet**. Fehler fallen sofort auf. | ✅ Die Microcloud-CI testet jeden Push und jeden Pull Request. |
| **Continuous Delivery (CD)** | Nach erfolgreichem Test wird automatisch ein **auslieferbares Ergebnis** bereitgestellt (z. B. ein Image in einer Registry). Das Ausrollen in den Betrieb wird manuell freigegeben. | ➖ Im Zusatz *cicd-test* (Release in die GitHub Container Registry) |
| **Continuous Deployment** | Jede Änderung, die alle Tests besteht, wird **automatisch in den Betrieb** ausgerollt, ohne manuelle Freigabe. | ➖ Nein, du rollst selbst mit `git pull` + `docker compose up -d` aus. |

## Vorteile von CI/CD

| Vorteil | Erklärung |
|---|---|
| Frühe Fehlererkennung | Automatische Tests finden Fehler direkt nach dem Push. |
| Schnelles Feedback | Nach wenigen Minuten weißt du, ob deine Änderung funktioniert. |
| Reproduzierbarkeit | Jeder Lauf startet in einer frischen, identischen Umgebung („bei mir geht's“ zählt nicht). |
| Dokumentation durch Code | Die Pipeline ist eine versionierte YAML-Datei im Repository. |

## Kernbegriffe von GitHub Actions

| Begriff | Bedeutung | Beispiel in `microcloud-ci.yml` |
|---|---|---|
| **Workflow** | Automatisierter Ablauf, definiert in einer YAML-Datei unter `.github/workflows/` | `name: Microcloud-CI` |
| **Event / Trigger** | Ereignis, das den Workflow startet | `on: push`, `pull_request`, `workflow_dispatch` |
| **Job** | Gruppe von Schritten, die auf **einem** Runner laufen | `stack-test` |
| **Step** | Einzelner Schritt in einem Job: Befehl (`run:`) oder Action (`uses:`) | `4 · Port 8085 prüfen` |
| **Action** | Wiederverwendbarer, fertiger Baustein | `actions/checkout@v7` |
| **Runner** | (Virtuelle) Maschine, die den Job ausführt | `runs-on: ubuntu-latest` |
| **Check** | Ergebnis eines Jobs, das an Commit und Pull Request angezeigt wird | ✅ *Compose-Stack testen (Port 8085)* |
| **Secret** | Vertrauliche Daten (Tokens, Passwörter), verschlüsselt im Repo hinterlegt | in diesem Workflow nicht nötig |

## Grundgerüst eines Workflows

```yaml
name: Mein Workflow          # Name
on:                          # WANN? (Events)
  push:
jobs:                        # WAS? (Jobs)
  test:
    runs-on: ubuntu-latest   # WO? (Runner)
    steps:                   # WIE? (Steps)
      - uses: actions/checkout@v7
      - run: echo "Hallo Welt"
```

## Alternativen zu GitHub Actions

GitLab CI/CD (in GitLab integriert, auch selbst hostbar), **Jenkins** (Open-Source-Klassiker, selbst gehostet), Forgejo/Gitea Actions (selbst gehostet, Syntax ähnlich wie GitHub Actions), Azure DevOps Pipelines.
