# 00 – Hausaufgabe: GitHub-Account vollständig vorbereiten

[⬅️ Zurück zum Projektauftrag](../README.md)

> **Bis zum Beginn der Projektwoche (KW47) erledigen!** Ohne fertigen Account kannst du in der Projektwoche nicht starten.
> Zeitbedarf: ca. 20 Minuten · Du brauchst: E-Mail-Postfach und Smartphone

## Warum das vorab?

GitHub verlangt von allen, die Code hochladen, eine **Zwei-Faktor-Authentifizierung (2FA)**. Das Einrichten dauert ein paar Minuten und braucht dein Smartphone. Im Unterricht wollen wir mit dem Projekt starten und keine Zeit mit Bestätigungsmails verlieren.

## Schritt 1: Account anlegen

1. Öffne <https://github.com/signup>.
2. Gib eine E-Mail-Adresse ein, auf die du **sicher Zugriff hast** (privat oder Ausbildungsbetrieb).
3. Wähle ein sicheres Passwort (Passwortmanager empfohlen).
4. Wähle einen **Benutzernamen**. Er wird Teil deiner Repo-Adressen (`github.com/BENUTZERNAME/...`) und ist öffentlich sichtbar. Wähle etwas Seriöses, das du auch in einer Bewerbung zeigen würdest.
5. Bestätige die E-Mail-Adresse über den Link bzw. Code in der Bestätigungsmail.
6. Fragen nach Plan oder Abo: Wähle **Free**. Du brauchst nichts Kostenpflichtiges.

> Du hast schon einen Account? Dann prüfe nur die Schritte 2 bis 4.

## Schritt 2: Zwei-Faktor-Authentifizierung (2FA) aktivieren

1. Installiere auf deinem Smartphone eine Authenticator-App, z. B. *2FAS*, *Aegis* (Android), *Ente Auth*, *Google Authenticator* oder *Microsoft Authenticator*.
2. Auf GitHub: oben rechts auf dein Profilbild → **Settings** → **Password and authentication** → **Enable two-factor authentication**.
3. Scanne den QR-Code mit der App und gib den 6-stelligen Code ein.
4. **Recovery-Codes sichern!** GitHub zeigt dir Wiederherstellungscodes an. Lade sie herunter und bewahre sie sicher auf (ausdrucken oder im Passwortmanager). Verlierst du dein Handy und hast keine Recovery-Codes, kommst du **nie wieder** in deinen Account.

## Schritt 3: E-Mail-Adresse privat halten

Jeder Commit enthält eine E-Mail-Adresse, und in öffentlichen Repos kann sie jeder lesen. GitHub stellt dir dafür eine anonyme Ersatzadresse bereit:

1. **Settings** → **Emails**.
2. Setze den Haken bei **Keep my email addresses private**.
3. Darunter steht deine Ersatzadresse im Format `12345678+benutzername@users.noreply.github.com`. **Notiere sie dir.** Du brauchst sie in Phase 1 für `git config`.

## Schritt 4: Profil vorbereiten

- **Settings** → **Public profile**: Ein Klarname ist nicht nötig. Vorname oder Kürzel reicht.
- Melde dich einmal ab und wieder an, um zu testen, ob 2FA funktioniert.

## Checkliste – bring das in die Projektwoche mit

- [ ] Ich kenne meinen GitHub-**Benutzernamen** und mein Passwort.
- [ ] Die E-Mail-Adresse ist bestätigt.
- [ ] 2FA ist aktiv, die Authenticator-App ist auf meinem Handy und das Handy ist geladen dabei.
- [ ] Meine Recovery-Codes sind gesichert.
- [ ] Ich habe meine `...@users.noreply.github.com`-Adresse notiert.

---

[⬅️ Projektauftrag](../README.md) · [➡️ Weiter: 01 – SSH-Schlüssel](01-ssh-key.md)
