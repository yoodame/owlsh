# owlsh — Development Notes

Single-file PWA: everything is in `index.html`.

## localStorage Schema

Data is stored under `owlsh_data` with a `_v` version field. On load, `migrateStore()` runs migrations sequentially from the stored version up to `STORE_VERSION`.

### Adding migrations

1. Bump `STORE_VERSION` (integer)
2. Add `MIGRATIONS[newVersion] = (store) => { ... }` — mutates store in place
3. Migration runs automatically on next page load for any user with older data

### What's safe without a migration

- Adding new words to existing `WORDS[level]` — mastery is keyed by word string, new words start unmastered
- Adding new `SENTENCES` entries
- Adding new game modes to `MODES` array (append only)
- Adding new fields to profile with defaults (check in code, don't assume field exists)

### What requires a migration

- Reordering or removing entries from `LEVELS` — `profile.level` is a numeric index
- Renaming a level `key` — word pools are keyed by level key
- Changing the shape of `profile.mastery` entries (currently `{ seen, correct, streak, stars }`)
- Removing or reordering `MODES` if any persisted state references mode by index
- Adding required fields to profile (migration should backfill existing profiles)

### Session state

`owlsh_session` stores the current game state for refresh persistence. It's ephemeral — cleared on logout. No versioning needed since stale session data just falls back to menu.

## Icons

Inline SVG paths in the `ICONS` object (Lucide-style, 24x24 viewBox). No external dependency. Use `icon(name, size, color)` helper to render. Add new icons by copying the path data from lucide.dev.

## Input modes

`S.inputMode` (`'tap'` | `'type'`) stored in `owlsh_inputMode`. Navigation screens are always tappable. Only game answer screens branch on mode — tap shows word cards / letter tiles, type shows the input bar.
