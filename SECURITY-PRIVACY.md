<!-- Copy of docs/security-privacy.md from the private source repository, refreshed with each release. Paths such as docs/… or src/… refer to that repository. -->

# Security and privacy

Written for hospital IT and data-protection reviewers. It describes what the
app does **today** and states limitations plainly. It is
updated whenever a security-relevant change lands.

## What RoundXer stores

Ward-round working notes for one clinician: patient identifiers (name, MRN,
bed, age, sex, nationality), admission details, history, problem lists,
investigation results, procedures, consult requests, progress notes, hospital
course and flags. Data model: `docs/data-model.md`.

## Where it is stored

- Only on the user's own device, in one SQLite database file inside the app's
  private storage.
- **No server, no cloud sync, no analytics, no crash reporting, no remote
  logging.** The phone app makes no network requests and works with the network
  off. Over-the-air update libraries (`expo-updates`) are deliberately not
  included. The one exception is on the desktop: **Check for updates** (Settings
  → About) goes online when the user presses it, and only then (see
  "Update check" below). No patient or personal data is sent.
- Android system backup is disabled for the app (`android:allowBackup="false"`),
  so neither the database nor key files are copied to Google device backups.
- Data leaves the device only when the user makes an encrypted `.roundxer`
  file and chooses where to send it (phone: the system share sheet; desktop:
  the user picks the folder in the system "Save as" window).

## Encryption at rest

- The database is encrypted with **SQLCipher 4** (AES-256), built into the app.
  The app refuses to open the database if SQLCipher is not present, so it
  cannot silently fall back to a plain file.
- The key is a random **256-bit data key** generated on the device at first
  launch with the platform's secure random generator. It never changes.
- The data key is stored in the operating system's keystore through
  `expo-secure-store` (Android Keystore-backed storage; iOS Keychain with
  `WHEN_UNLOCKED_THIS_DEVICE_ONLY` — never synced or restored to another device).
- The app uses a single database connection; every access goes through it.

## iPhone specifics

- The data key and PIN hash are in the iOS Keychain with
  `WHEN_UNLOCKED_THIS_DEVICE_ONLY`: never in iCloud Keychain or an iCloud /
  iTunes backup restored to another device.
- iOS keeps Keychain entries after an app is deleted. When RoundXer starts
  with no database and no `keys.json` (a reinstall), it removes its old
  Keychain entries before setup, so nothing from the deleted install lingers.
  It never does this while a database exists.
- The app's files (encrypted database, `keys.json`, `setup.json`, safety copy)
  are **excluded from iCloud and Finder backups** (the Documents folder is marked
  "do not back up" at every start), matching Android, where app backup is off.
  So they never reach Apple's servers; moving data to a new iPhone is done with
  a `.roundxer` backup file. (Built 29/09/2026; to be confirmed on the first
  iPhone build.)
- A file opened with "Open in RoundXer" is copied by iOS into the app's
  Documents/Inbox folder; RoundXer deletes that copy after reading it (and any
  leftovers at start-up).
- The app declares a privacy manifest: no tracking, no data collected, and the
  standard reasons for the system APIs React Native uses (settings storage, file
  dates, uptime, free disk space).

## Desktop app (Windows and macOS)

- Same code as the phone, running as the app's web build inside a Tauri 2
  window (WebView2 on Windows, WKWebView on macOS). The database is the same
  SQLCipher 4 format, opened by the desktop program itself (Rust, rusqlite with
  bundled SQLCipher), in the app's private folder:
  `%LOCALAPPDATA%\com.alruwaili.roundxer` on Windows (the *Local* profile
  folder, which Windows roaming profiles do not copy to a server) and
  `~/Library/Application Support/com.alruwaili.roundxer` on macOS.
- **The data key is kept in the operating system's keychain** — Windows
  Credential Manager (protected by the user's Windows login) or the macOS
  Keychain — never in the app folder. RoundXer opens with the PIN, like the
  phone. If the keychain loses the entry or refuses access (e.g. the user clicks
  Deny on a Mac), the passphrase or recovery key opens the data again. There are
  no biometrics on the desktop yet. (Until 28/09/2026 test builds asked for the
  passphrase at every start instead; the user found that too much.)
- Auto-lock on the desktop also counts time **without mouse or keyboard use**
  (1, 2, 5 or 15 minutes), not only time in the background.
- Crypto uses the web view's built-in WebCrypto (AES-256-GCM, PBKDF2-SHA256):
  same algorithms and file format as the phone, verified by tests in both
  directions.
- **No network from the window:** it runs under a Content Security Policy that
  allows loading only the app's own bundled files and talking only to the
  desktop program (`connect-src ipc:`); any attempt to reach the internet is
  blocked by the web view. The program contains no telemetry or crash-report
  code. Its only network code is the update check below.
- **Update check (added 29/09/2026, at the user's request):** nothing runs on
  its own. When the user presses **Check for updates**, the desktop program
  requests `latest.json` from the public RoundXer releases page on GitHub
  (`github.com/alruwaili966/RoundXer-releases`). The request carries only
  what any web request carries (the computer's IP address, the app version and
  platform); nothing from the database. If a newer version is listed and the
  user confirms, the program downloads that installer and checks its
  **signature** (Ed25519 / minisign) against the public key built into the app
  before installing it; a download that fails the check is refused and nothing
  changes. The signing key is kept offline by the developer (never in the
  source repository). Windows: the installer replaces the program and reopens
  RoundXer; macOS: the app is replaced and restarted. Patient data, keys and
  settings are not touched by an update.
- The web side can only ask the desktop program for fixed files (the database,
  `keys.json`, `secrets.json` with the PIN hash, the safety copy) and to save an
  export; the desktop program then shows the system "Save as" window and writes
  only to the path the user picks there. The web side cannot name any path. A `.roundxer` file the
  user opens (double-click, "Open with", drag onto the window) is read only
  because the operating system handed that exact file to the app.
- The browser context menu (with "Save as…" / "Print") is disabled outside text
  fields, and Ctrl/Cmd+S and Ctrl/Cmd+P do nothing.
- The database commands refuse SQL that could reach other files or keys
  (ATTACH, VACUUM INTO, `sqlcipher_export`, key/rekey/cipher pragmas); the
  SQLite attach limit is 0.
- While the lock screen shows, the app underneath is inert: no focus, clicks or
  keys reach it, PIN digits go only to the lock screen, and a file opened
  meanwhile waits until the app is unlocked. "Auto-lock: Immediately" locks as
  soon as the window loses focus.
- Opening the web build in an ordinary browser shows only "RoundXer's data
  opens only in the RoundXer desktop app".

## Passphrase and recovery key

**Optional** (user decision 28/09/2026). Offered on first launch ("Later"
skips it); otherwise the first **Back up** asks for it, or Settings →
Security → Set passphrase. The database is encrypted either way — the
passphrase protects backup files and lets the user rescue the data key.

- **Passphrase:** chosen by the user, at least 12 characters (no other rules).
  It is never stored.
- **Recovery key:** 32 random characters (160 bits), created with the
  passphrase and shown once as 8 groups of 4, which the user writes down
  (confirmed with a tick box). It is never stored.
- The data key is **wrapped** (encrypted) twice with AES-256-GCM: once under a
  key derived from the passphrase and once under a key derived from the
  recovery key (PBKDF2-SHA256, 600 000 iterations, random 16-byte salt,
  random 12-byte nonce, purpose-bound associated data).
- The two wrapped copies are kept in the keystore **and** in a file
  (`keys.json`) in the app's private storage. The file contains no usable key
  without the passphrase or recovery key; it lets the user restore access if
  the keystore entry is ever lost. From slice 6 the same wrapped copies travel
  in every `.roundxer` backup header, so a backup opens on another device with
  either secret.
- Changing the passphrase or creating a new recovery key re-wraps the same data
  key. Backups made earlier still open with the secrets that were current then.
- If the keystore loses the data key, the app shows "Unlock your data" and asks
  for the passphrase or recovery key. It **never deletes or overwrites** the
  database.
- **Without a passphrase** the data key exists only in the keystore (phone
  secure storage / computer keychain). If it is lost, the data on that device
  cannot be opened by anyone; the app says so and offers **Try again** and
  **Start fresh**, then restoring the latest backup file. Start fresh never
  deletes anything: the database is renamed `roundxer.db.set-aside-<time>`
  (with its `-wal` / `-shm`), its data key is moved to a separate keystore /
  keychain entry (`rx_data_key_set_aside_v1` / `data-key-set-aside`, last
  five kept) and any wrapped copies to `keys.set-aside.json`; then setup
  starts again. RoundXer itself has no way to reopen set-aside data.
- **Device backups:** Android backups of RoundXer are switched off
  (`allowBackup: false`). A computer backup (Time Machine, File History) and,
  once there is an iPhone build, an iCloud / Finder backup can include the
  **encrypted** database and `keys.json`; the data key itself stays in the
  keychain (iPhone: this device only). Anyone with such a backup still needs the
  passphrase or recovery key. Excluding these files from iPhone backups is
  planned for the iOS build (KI-013).
- A keystore / keychain that **can't be read** (for example a refused macOS
  prompt) is never treated as a lost key: with a passphrase the passphrase
  screen opens the data; without one the app shows the error with **Try
  again**, and Start fresh refuses to run while the key can't be read. A setup-done marker (keystore + `setup.json`) makes sure a lost key is
  never mistaken for a first run. On the desktop, setup refuses to finish
  without a passphrase if the keychain did not keep the key.

## Backup and transfer files (`.roundxer`)

Format: `docs/backup-format.md`.

- **Back up** (for the user's own devices): the file is encrypted with
  AES-256-GCM under the device's data key; the header carries the same
  passphrase- and recovery-wrapped copies as the device, so the file opens only
  with the user's passphrase or recovery key.
- **Share** (for a colleague): the file is encrypted under a fresh random key
  wrapped only by a one-time passphrase the user types (at least 10 characters;
  case, spaces and dashes ignored so it can be read out). The user's own keys
  are not in the file. The user must pass the passphrase by another channel.
- The whole header is authenticated with the data: any change is detected.
- Files contain deleted records (tombstones) so deletions sync; settings are
  never included.
- Phone: exported files are written to the app cache only long enough to
  share and are deleted on the next app start. Desktop: the file is saved
  where the user picks in the "Save as" window and stays there until the user
  moves or deletes it.
  Where the user sends it (Quick Share, AirDrop, Drive, email, USB stick) is
  their choice and responsibility; the file stays encrypted.
- **Back up needs a passphrase.** Without one, the first Back up asks the user
  to create it (and shows the recovery key) before making the file. Share
  (one-time passphrase) works without it.
- **Import** asks for the passphrase / recovery key, shows a preview, and
  merges by the rules in `docs/backup-format.md`. **Replace everything**
  requires typing REPLACE and first saves an encrypted safety copy of the
  current data on the device (latest one only), which can be restored. The
  safety copy is encrypted with the device's own data key only (protection
  `device`), so it opens only on that install.
- **Handover PDF / text, exported notes:** plain (not encrypted) documents the user chooses to send (share sheet, clipboard, print). The phone deletes its cached PDF on the next start. Handover edits stay on the device (a local draft for the day, never in backups).
- **Backup reminder (phone):** an optional daily local notification ("Time to
  back up your patient list") — never containing patient information. The
  desktop shows the last backup time on the Areas screen instead.

## App lock

- Modes (Settings → Security): **Off**, **Biometric** (fingerprint/face with a
  6-digit PIN as fallback), **PIN** (6 digits). Optional at setup ("Later"
  leaves it Off); it can be turned on at any time.
- The lock screen shows only the app name and the PIN pad / biometric prompt.
  The app is locked after every cold start, and after it has been in the
  background for the chosen time (immediately, 1, 2 (default), 5 or 15 minutes).
- The PIN is stored only as a salted PBKDF2-SHA256 hash (200 000 iterations)
  in the keystore. After 5 wrong PINs the app makes the user wait (30 s, then
  1, 5, 15 minutes, then 1 hour per attempt). The counter survives restarts.
  Wrong PINs **never erase data**.
- "Forgot PIN?" accepts the passphrase or recovery key, then asks for a new PIN.
  **Without a passphrase:** on a phone, the phone's own screen lock
  (fingerprint, face or the phone's PIN/pattern) confirms the owner, then a new
  PIN is chosen — so anyone who knows the phone's own PIN can reset RoundXer's
  PIN. On a computer there is no reset: only Start fresh (above) and restoring
  a backup. The app warns about this when a PIN is chosen without a passphrase.
- Unlocking uses the operating system's biometric prompt
  (`expo-local-authentication`) with no device-passcode fallback (the phone's
  passcode is accepted only for the Forgot-PIN reset above). The app never
  sees fingerprint or face data.
- Changing or switching off the lock requires the current PIN.
- While the lock screen shows, open forms and dialogs are hidden too (they
  would otherwise stay on top of the lock screen — fixed in slice 7, KI-008).

## Retention and auto-delete

- Off by default: discharged patients stay until the user deletes them.
- When on (7, 30 or 90 days after discharge), the app deletes due patients each
  time it opens: all their notes, problems, results, procedures, consults,
  hospital course and flag history are erased, and the patient row is reduced to
  an anonymous marker (no name, MRN or clinical text) so a backup from another
  device cannot bring the patient back and the deletion reaches that device on
  the next merge. A notification (no names) warns a day before; any patient can
  be kept. Turning it on says how many patients would be deleted immediately.
- **Recently deleted** (patients deleted by hand) is emptied automatically:
  anything deleted more than 30 days ago (7 / 30 / 90, Settings → Recently
  deleted) is erased the same way when the app opens; **Empty now** erases
  everything there at once after a confirmation.

## Current limitations

- **The app lock is an in-app gate.** The data key sits in the keystore where
  the app can read it without a fingerprint; the lock stops people using the
  app UI, not an attacker who can run code as the app on an unlocked, rooted
  or compromised phone. Binding the key to biometrics was considered and
  deferred: a new fingerprint enrolment would invalidate it and PIN mode could
  not use it.
- **Screenshots are allowed.** The app does not set Android `FLAG_SECURE`, so
  screenshots, screen recording and the recent-apps preview can show patient
  data (user decision, slice 2). Users should not screenshot real patient data.
- The passphrase protects backup files that may travel by email or cloud
  drives. Its strength is the user's responsibility beyond the 12-character minimum.
- Desktop: no Windows Hello / Touch ID yet (PIN only). As on the phone, the PIN
  is an in-app gate: someone who can run programs as the user on their
  unlocked, logged-in computer could read the key from the keychain. Exports
  saved on the computer stay there until the user removes them (backups are
  encrypted; handover PDFs are not).
  Desktop reminders (backup, follow-ups) only appear while RoundXer is open.
- Not yet in Google Play or TestFlight. Beta installers for colleagues are on a
  public releases page (installers and notes only, no source code) with SHA-256
  checksums. The Android app is signed with the developer's own release key.
  The Mac app is signed with the developer's Apple Developer ID (from 0.1.1) but
  not yet notarised, so macOS still asks once ("Open Anyway"). The Windows
  installer is not code-signed, so SmartScreen warns on first run.

## Planned (later slices)

Signed builds (Apple Developer ID + notarisation, Windows code signing),
Google Play testing and TestFlight for iPhone (slice 17). Auto-delete of
discharged patients is built (see Retention and auto-delete).

## Threat model (summary)

| Threat | Mitigation now | Later |
|---|---|---|
| Lost/stolen phone, locked | OS lock; database encrypted with a keystore-held key | — |
| Lost/stolen phone, unlocked | App lock (PIN/biometric) + auto-lock | — |
| Lost/stolen laptop, or copy of its app folder | Database SQLCipher-encrypted; data key only in the OS keychain (tied to the user's login), not in the app folder; `keys.json` needs the passphrase or recovery key | Touch ID / Windows Hello (optional) |
| Unattended open desktop app | PIN auto-lock after inactivity; forms hidden under the lock | — |
| Someone shoulder-surfs the PIN | Biometric mode; wait after wrong PINs | — |
| Copy of the app's files without the keystore | Database is SQLCipher-encrypted; `keys.json` needs the passphrase or recovery key (PBKDF2 600 000); Android backup disabled | — |
| Keystore loses the key | Restore with passphrase or recovery key; data never deleted | — |
| Data sent to a server | Phone: no network code at all. Desktop: only the update check, on request, which sends no data | Hospital-approved server only if the hospital asks |
| Tampered or fake update | Desktop installs only updates signed with the developer's offline key (public key built into the app); HTTPS to GitHub | Apple / Microsoft code signing (slice 17) |
| Backup file intercepted | AES-256-GCM `.roundxer`; key wrapped by passphrase / recovery key (PBKDF2 600 000), or a one-time passphrase for Share | — |
| Wrong import wipes data | Merge never deletes local-only rows; Replace needs "REPLACE" and keeps a safety copy | — |
