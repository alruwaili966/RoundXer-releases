# RoundXer — user guide

RoundXer replaces the paper patient list on ward rounds: a list per area,
a page per patient, progress notes, hospital course, follow-ups and handover.
Everything stays **on your own device, encrypted**. There is no account, no
server and nothing is sent anywhere unless you send a file yourself.

> For colleagues. Installers: see [Releases](../../releases) (latest).
> Store links and signed installers come later.

## 1. Install

| Device | How |
|---|---|
| iPhone | Later: TestFlight invitation → install **TestFlight** from the App Store → open the invitation → Install. |
| Android | Google Play testing link (once available). Until then: the `RoundXer_<version>_android.apk` file from the releases page — open it on the phone and allow "Install unknown apps" for the app you opened it with, only for this install. |
| Windows | Run `RoundXer_<version>_windows_x64-setup.exe`. If Windows says "Windows protected your PC": **More info → Run anyway** (the installer is not signed yet). Installs for your user only, no admin rights needed. |
| Mac | Apple-chip Macs (M1 or later). Open the `.dmg`, drag **RoundXer** to Applications. The first time, macOS says it "could not verify" RoundXer, because it is not yet signed by Apple: click **Done**, open System Settings → Privacy & Security, scroll to Security and click **Open Anyway**, then confirm with your Mac password or Touch ID. Only needed once. |

**Updating.** Install the new version over the old one; your data, settings and
PIN stay. On a computer (from version 0.1.1): Settings → About → **Check for
updates** → **Install update**. RoundXer closes, installs it and opens again.
This is the only time RoundXer goes online, and only when you press the button.

## 2. First start (about 2 minutes)

The welcome screen says what RoundXer is, how it keeps your data private, and
what you are responsible for (section 5). You can read it again any time in
Settings → About RoundXer. Then:

Both steps are optional — tap **Later** to skip, and set them any time in
Settings → Security.

1. **Passphrase** — at least 12 characters; a few unrelated words work well.
   It opens your backups on other devices. It is never stored anywhere. If you
   skip it, RoundXer asks for it the first time you make a backup.
   With it comes a **recovery key** — 8 groups of 4 characters, shown **once**.
   Write it on paper and keep it away from your phone or laptop.
2. **App lock** — fingerprint / face with a 6-digit PIN backup (phone), or a PIN.

If you forget the passphrase, the recovery key still opens your data and your
backups. If you lose both, nobody (including the developer) can open them.

Without a passphrase: if you forget the PIN, a phone lets you reset it with the
phone's own screen lock; a computer can't reset it — RoundXer can only start
fresh, and you restore your latest backup. And if the phone or computer ever
loses the key to your data, the data there can't be opened: start fresh and
restore a backup. Setting a passphrase avoids both.

On a **computer**, the key is kept in the Windows / macOS keychain, so
RoundXer opens with the PIN (or straight away if the lock is off).

## 3. Daily use

- **Areas** — one card per ward or unit you round on, with seen progress and
  flags. Search finds patients by name (Arabic spellings too), MRN or bed.
- **Patient list** — flagged patients first (Unstable, then Follow-up), then
  to see, then seen. Tap the circle to mark **seen** (resets each night).
  Hold and drag a card to change the round order.
- **Patient page** — history at the top, flags, today's notes, problems,
  investigations, procedures, consults, hospital course. Swipe or ‹ › to move
  through the round. On a wide computer window the page opens beside the list.
- **Notes** — several per day; a new note starts from your last one ("Start
  blank" to clear). Saved as you type. Use your keyboard's microphone to dictate.
- **Export note** — fill a template (Daily progress, Brief, Discharge summary or
  your own) and Share / Copy it.
- **Smart phrases** — Settings → Notes: `.dapt` + space expands to your text.
- **Follow-ups** — every open follow-up across areas with due times; you get a
  notification when one is due (phone).
- **Handover** — I-PASS per area, generated from the list; edit, then PDF or text.
- **Present** — one patient per screen in large type for the round.
- **Discharged patients** — search, readmit. Optional **auto-delete** after 7,
  30 or 90 days (Settings → Privacy; off unless you turn it on).

## 4. Backups and moving data

- **Back up** (Areas banner or Settings) makes an encrypted `.roundxer` file.
  Send it to your own other device (Quick Share, AirDrop, cable — or Drive / email
  if your hospital allows encrypted files there)
  and open it there → **Import** → your passphrase or recovery key → **Merge**.
  Back up regularly: if your phone is lost, the latest backup is all there is.
- **Share** gives a colleague selected patients: choose **One-time**
  passphrase, send the file, and tell them the passphrase separately (e.g. by
  phone). Your own passphrase is never in a shared file.
- **Merge** adds and updates; it never removes what is only on this device.
  **Replace everything** wipes this device first (a safety copy is kept).

## 5. Your responsibilities

- RoundXer holds identifiable patient data. Follow your hospital's rules on
  what you may record on a personal device and for how long.
- Keep your device locked with a PIN/passcode of its own; keep RoundXer's lock on.
- Don't take screenshots of real patient data — they are not encrypted.
- Exported text, PDFs and printouts are **not** encrypted: share them only
  through channels your hospital allows, and delete them when done.
- Keep the recovery key private and on paper. Never send it by message.
- If a device is lost, tell your hospital's information-governance contact
  according to local policy. Your data on it stays encrypted.

More detail for IT and data-protection reviewers: [SECURITY-PRIVACY.md](SECURITY-PRIVACY.md).
