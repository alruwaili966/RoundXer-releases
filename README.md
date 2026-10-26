# RoundXer — downloads

RoundXer replaces the paper patient list on ward rounds: patients by area,
progress notes, problems and results, hospital course, follow-ups and
handover. Everything stays **on your own device, encrypted**. There is no
account and no server, and nothing is sent anywhere unless you send a file yourself.

Website: **https://roundxer.com**. This repository holds **installers and release notes only**. There is no source code here.

> **Beta.** The Mac app is signed and notarised by Apple (from 0.1.2). The
> Windows installer is not yet code-signed, so Windows warns you the first time; the
> steps below get past it. Android: [Google Play](https://play.google.com/store/apps/details?id=com.alruwaili.roundxer).
> iPhone and iPad: [App Store](https://apps.apple.com/app/roundxer/id6817611660).

## Download

Open **[Releases](../../releases)** and take the latest version. Pick the file for your device:

| Device | File |
|---|---|
| Android phone | [Google Play](https://play.google.com/store/apps/details?id=com.alruwaili.roundxer) |
| Windows 10 / 11 | `RoundXer_<version>_windows_x64-setup.exe` |
| Mac with Apple chip (M1 or later) | `RoundXer_<version>_mac_apple-chip.dmg` |
| iPhone / iPad | [App Store](https://apps.apple.com/app/roundxer/id6817611660) |

## Install

**Android**

Get it from **[Google Play](https://play.google.com/store/apps/details?id=com.alruwaili.roundxer)**. Updates arrive through Google Play.

**Windows**
1. Run the `setup.exe` file. It installs for your Windows user only and needs no admin rights.
2. If Windows says "Windows protected your PC", click **More info**, then **Run anyway**.

**Mac**
1. Open the `.dmg` file and drag **RoundXer** to Applications. Open it.
2. If macOS asks to let RoundXer use the keychain, choose **Always Allow**.

Versions before 0.1.2 were not notarised: macOS said it "could not verify" RoundXer. Then click **Done**, open System Settings → Privacy & Security and click **Open Anyway** (once).

## Update

Install the new version over the old one. Your patients, settings and PIN stay.

**Computer, from version 0.1.1:** Settings → About → **Check for updates** → **Install update**. RoundXer downloads the new version, checks its signature, installs it and opens again. Version 0.1.0 has no such button, so install 0.1.1 by hand once.

By hand:
- **Android:** Google Play updates RoundXer.
- **Windows:** run the new `setup.exe`.
- **Mac:** drag the new app to Applications and replace the old one.

Make a backup first anyway (Areas → **Back up**).

## Check the download (optional)

Each release lists a SHA-256 checksum for every file. To check a file, compare its checksum with the listed one:
- **Windows (PowerShell):** `Get-FileHash .\RoundXer_…-setup.exe`
- **Mac (Terminal):** `shasum -a 256 ~/Downloads/RoundXer_….dmg`

## Privacy, in short

- Your data stays on your device, in an encrypted database (SQLCipher, AES-256). The phone app never uses the network (the Android app has no Internet permission). The computer app goes online only when you press **Check for updates**, and sends nothing about you or your patients.
- Backups are encrypted `.roundxer` files. You choose where to send them.
- Exported text, PDFs and screenshots are **not** encrypted. Share them only through channels your hospital allows.
- RoundXer holds identifiable patient data. Follow your hospital's rules on what you may record on a personal device.

For more detail, see the [user guide](USER-GUIDE.md) and [security and privacy](SECURITY-PRIVACY.md), which is written for hospital IT reviewers.

## Questions and problems

Email **support@roundxer.com** or see https://roundxer.com/support. **Never send or post patient details**, including screenshots or files.
