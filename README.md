# Recently Deleted

A macOS menu bar app for watching — and clearing — the Messages "Recently
Deleted" queue.

Deleting a lot of messages leaves them sitting in Messages' Recently Deleted
queue, clearing slowly as iCloud syncs, with no way to see how far along it is.
This puts the running total next to your clock, and a per-conversation
breakdown with real contact names behind a click.

```
menu bar:   🗑 1,284

┌──────────────────────────────────┐
│ Recently Deleted        1,284    │
│ ▼ 312 cleared since launch       │
├──────────────────────────────────┤
│ Mom                        408   │
│ Alex, Jordan, Sam          291   │
│ +1 415 555 1234            173   │
│ Work Thread                 96   │
├──────────────────────────────────┤
│ iCloud          Synced just now  │
│ Messages               124,057   │
│ Delete records          58,914   │
│ On this Mac            103,018   │
└──────────────────────────────────┘
```

Refreshes once a second, opens at login, no Dock icon and no window.

This repository is for distribution only — downloads live under
[Releases](../../releases).

## What it does

- **Left click** the menu bar item for the per-conversation breakdown, plus
  what iCloud reports: total messages stored, delete records, and how many this
  Mac holds locally, with the last sync time beside them.
- **Right click** for:
  - **Sync Now** — triggers a Messages iCloud sync. There is no API for this,
    so it drives the button in System Settings.
  - **Empty Recently Deleted (N)…** — permanently deletes everything in the
    queue, after asking and naming the exact count.
  - Open Messages, and Quit.

## Install

1. Download the zip from [Releases](../../releases), unzip it, and drag
   `Recently Deleted.app` into `/Applications`.
2. The first launch is blocked with *"Apple cannot check it for malicious
   software"* — the app is ad-hoc signed rather than notarized, since
   notarization requires a paid Apple Developer account. Open it, dismiss the
   warning, then go to **System Settings → Privacy & Security** and click
   **Open Anyway**.

   Or from a terminal:

   ```sh
   xattr -dr com.apple.quarantine "/Applications/Recently Deleted.app"
   ```
3. Grant **Full Disk Access**: System Settings → Privacy & Security → Full Disk
   Access, then add or enable `Recently Deleted`. The Messages database is
   protected by macOS, so until you do this the menu bar shows `—`.
4. Only if you want Sync Now or Empty Recently Deleted, also grant
   **Accessibility** in the same settings pane. Both work by clicking through
   real UI, which macOS gates behind that permission. The counts do not need
   it.

### After an update

Both permissions are tied to the app's exact code signature, which changes
every time the app is rebuilt. So **updating revokes them**.

Full Disk Access flips visibly to denied. Accessibility does not — the entry
keeps showing as enabled while every call is refused, which surfaces as
unrelated failures. So after an update, **remove the entry with the minus
button and add it back** rather than toggling it, then quit and reopen the app:
a running process does not pick up a new grant.

## Privacy

The app reads two things, both local, both read-only:

- `~/Library/Messages/chat.db` — opened with `SQLITE_OPEN_READONLY`, for the
  Recently Deleted counts.
- The AddressBook databases under `~/Library/Application Support/AddressBook/`
  — to turn phone numbers into contact names.

It also reads Messages' own `com.apple.madrid` preferences for the iCloud
figures, which needs no permission at all.

Nothing is sent anywhere. There is no networking code in the app, and you can
check that yourself without taking my word for it:

```sh
otool -L "/Applications/Recently Deleted.app/Contents/MacOS/RecentlyDeletedBar"
```

The only non-UI library it links is `libsqlite3.dylib`. No `CFNetwork`, no
`Network.framework`. A network monitor like Little Snitch or LuLu will show it
making no connections at all.

Emptying Recently Deleted is deliberately done through the Messages UI rather
than by writing to the database. A direct delete would never reach iCloud and
would leave the local store disagreeing with the server.

Full Disk Access is a broad permission, Accessibility lets an app drive other
apps, and this is an unsigned binary from a stranger. Those facts together are
worth a moment's thought before installing. If you would rather not, that is an
entirely reasonable call.

## Requirements

macOS 14 or later, Apple silicon.

## License

[MIT](LICENSE).
