# RoundXer privacy policy

Last updated: 30/09/2026 (Android: no Internet permission)

RoundXer is a ward-round notebook for clinicians. It is made by Abdulrahman Alruwaili.

## What RoundXer collects

**Nothing.** RoundXer has no accounts, no server, no analytics, no advertising, no tracking and no crash reporting. The developer never receives any data from the app.

## Where your data is kept

- Everything you enter (patients, notes, problems, results and settings) stays on your own device, in an encrypted database (SQLCipher, AES-256).
- The encryption key is kept in the device's own secure storage (Android Keystore, iOS Keychain, Windows Credential Manager or the macOS Keychain).
- On iPhone and Android, RoundXer's files are excluded from cloud and device backups.

## When data leaves your device

Only when you choose to send it:
- **Backups** are encrypted `.roundxer` files. You decide where to save or send them.
- **Exported text and PDFs** (notes, handover) are not encrypted. You decide where they go.

## Network use

- The phone app (Android and iPhone) never uses the network. The Android app does not even have the Internet permission, so Android itself blocks any connection.
- The computer app (Windows and Mac) goes online only when you press **Check for updates** in Settings → About. It then asks this releases page on GitHub for a newer version. Nothing about you or your patients is sent. GitHub sees the request like any web request (for example your IP address); see GitHub's own privacy statement.

## Your responsibilities

RoundXer holds identifiable patient information. Follow your hospital's rules on what you may record on a personal device and for how long. Keep your device locked, and share exports only through channels your hospital allows.

## Deleting your data

Delete a patient in the app. Uninstalling RoundXer from a phone removes its data. On a computer, uninstalling leaves the encrypted data folder behind; delete it yourself (Windows: `%LOCALAPPDATA%\com.alruwaili.roundxer`, Mac: `~/Library/Application Support/com.alruwaili.roundxer`). Backup files you made are not affected; delete them yourself.

## Changes

Changes to this policy are published on this page.
