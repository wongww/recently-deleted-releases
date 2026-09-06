# Recently Deleted

A macOS menu bar app for watching the Messages "Recently Deleted" queue drain.

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
└──────────────────────────────────┘
```

Refreshes once a second, opens at login, no Dock icon and no window.

This repository is for distribution only — downloads live under
[Releases](../../releases).

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
   Access, then add or enable `Recently Deleted`.

Step 3 is required because the Messages database is protected by macOS. Until
you do it the menu bar shows `—`, and the panel says so with a button that
opens the right settings pane.

## Using it

- **Left click** the menu bar item for the breakdown.
- **Right click** for Open Messages and Quit.
- It registers itself to open at login.

## Privacy

The app reads two things, both local, both read-only:

- `~/Library/Messages/chat.db` — opened with `SQLITE_OPEN_READONLY`, for the
  Recently Deleted counts.
- The AddressBook databases under `~/Library/Application Support/AddressBook/`
  — to turn phone numbers into contact names.

Nothing is sent anywhere. There is no networking code in the app, and you can
check that yourself without taking my word for it:

```sh
otool -L "/Applications/Recently Deleted.app/Contents/MacOS/RecentlyDeletedBar"
```

The only non-UI library it links is `libsqlite3.dylib`. No `CFNetwork`, no
`Network.framework`. A network monitor like Little Snitch or LuLu will show it
making no connections at all.

Full Disk Access is a broad permission, and installing an unsigned binary from
a stranger is a real decision — those two facts together are worth a moment's
thought before step 3. If you'd rather not, that's an entirely reasonable call.

## Requirements

macOS 14 or later, Apple silicon.

## Notes

Full Disk Access is tied to the app's exact code signature, so **updating to a
new version revokes it**. macOS flips the entry to denied rather than
re-prompting. Switch it back on after installing an update — the entry is
already in the list.

## License

[MIT](LICENSE).
