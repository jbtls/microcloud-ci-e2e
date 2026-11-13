# 01 – Git einrichten und SSH-Schlüssel für GitHub erzeugen

[⬅️ Zurück zum Projektauftrag](../README.md)

> **Ziel:** Dein LXC-Container darf per SSH-Schlüssel in deine GitHub-Repositories pushen.
> **Wo?** Alle Befehle laufen **auf deinem LXC-Container** (per SSH verbunden wie in Modul 2–4).
> **Zeit:** ca. 30 Minuten

## Kurz erklärt: Warum ein SSH-Schlüssel?

Aus KW35 kennst du das Prinzip: Statt eines Passworts beweist dein Rechner mit einem **Schlüsselpaar**, wer er ist.

- Der **private Schlüssel** (`~/.ssh/id_ed25519`) bleibt auf deinem LXC und wird **niemals** weitergegeben.
- Der **öffentliche Schlüssel** (`~/.ssh/id_ed25519.pub`) wird bei GitHub hinterlegt.

Diesmal ist **GitHub der Server** und **dein LXC der Client**, also genau umgekehrt wie bei deinem eigenen SSH-Zugang. Mit Passwort kannst du übrigens gar nicht pushen: GitHub akzeptiert auf der Kommandozeile keine Kontopasswörter.

## Schritt 1: Git prüfen und einrichten

```bash
git --version || sudo apt install -y git
```

Sage Git, wer du bist. Jeder Commit wird mit diesen Angaben gestempelt. Verwende die **noreply-Adresse** aus der Hausaufgabe:

```bash
git config --global user.name "Vorname K."
git config --global user.email "12345678+benutzername@users.noreply.github.com"
git config --global init.defaultBranch main
git config --global pull.rebase false
git config --list --global
```

**Checkpoint:** Die letzte Ausgabe zeigt `user.name`, `user.email`, `init.defaultbranch=main` und `pull.rebase=false`.

## Schritt 2: Schlüsselpaar erzeugen

```bash
ssh-keygen -t ed25519 -C "LXC microcloud - benutzername"
```

- Speicherort: **Enter** drücken (Standard `~/.ssh/id_ed25519` übernehmen).
- Passphrase: Du darfst eine vergeben (sicherer). Dann fragt Git bei jedem Push danach. Für diese Projektwoche ist auch **Enter ohne Passphrase** in Ordnung.

> Meldung `... already exists. Overwrite (y/n)?` → **n** eingeben! Du hast schon einen Schlüssel und nutzt einfach diesen.

## Schritt 3: Öffentlichen Schlüssel bei GitHub hinterlegen

1. Öffentlichen Schlüssel anzeigen (**.pub!**):

   ```bash
   cat ~/.ssh/id_ed25519.pub
   ```

   Die Ausgabe ist **eine** Zeile, die mit `ssh-ed25519 AAAA...` beginnt. Markiere die ganze Zeile und kopiere sie.
2. GitHub → Profilbild → **Settings** → **SSH and GPG keys** → **New SSH key**.
3. *Title:* z. B. `LXC microcloud`, *Key type:* `Authentication Key`, *Key:* Zeile einfügen → **Add SSH key** (ggf. mit 2FA bestätigen).

## Schritt 4: Verbindung testen

```bash
ssh -T git@github.com
```

Beim ersten Mal fragt SSH nach dem Fingerabdruck von GitHub (*Trust on First Use*, kennst du aus KW35). Vergleiche ihn mit den offiziellen Werten unter <https://docs.github.com/de/authentication/keeping-your-account-and-data-secure/githubs-ssh-key-fingerprints> und bestätige mit `yes`.

**Checkpoint – so sieht Erfolg aus:**

```text
Hi benutzername! You've successfully authenticated, but GitHub does not provide shell access.
```

## Falls Port 22 gesperrt ist

Bleibt `ssh -T git@github.com` hängen oder meldet `Connection timed out`, blockiert eine Firewall den SSH-Port 22. GitHub bietet SSH zusätzlich über Port **443** an:

```bash
nano ~/.ssh/config
```

```text
Host github.com
    Hostname ssh.github.com
    Port 443
    User git
```

Danach erneut `ssh -T git@github.com` testen.

## Kontrollfragen

1. Welche der beiden Dateien hast du bei GitHub hochgeladen, und warum auf keinen Fall die andere?
2. Warum funktioniert `ssh -T git@github.com`, obwohl du nirgends ein Passwort eingegeben hast?

<details>
<summary>Lösung anzeigen</summary>

1. Hochgeladen wird nur `id_ed25519.pub`, der **öffentliche** Schlüssel. Mit dem privaten Schlüssel `id_ed25519` könnte sich jeder als du ausgeben. Er verlässt deinen LXC nie.
2. GitHub schickt eine Aufgabe (Challenge), die nur mit dem passenden privaten Schlüssel gelöst werden kann. Dein LXC löst sie lokal, GitHub prüft die Antwort mit dem hinterlegten öffentlichen Schlüssel. Ein Passwort wird dabei nie übertragen.

</details>

---

[⬅️ 00 – Hausaufgabe](00-hausaufgabe-github-account.md) · [➡️ Weiter: 02 – Repo anlegen & Stack einspielen](02-repo-anlegen-und-stack-einspielen.md)
