# Covenant — Prayer Journal · SPEC v1.2

> **This document is the single source of truth for the Covenant app.**
> Upload this file alongside index.html at the start of every Claude session.
> Claude will update this file directly — you do not need to edit it manually.

---

## Purpose

A personal prayer and faith journal that goes beyond tracking prayer requests — it captures God's faithfulness over time, extracts personal wisdom from lived experience, and builds a searchable spiritual record tied to Scripture.

The core difference from existing apps: prayers are *living threads* that evolve over time, not static list items. AI assists with processing raw thoughts, drafting journal entries, generating living summaries, and suggesting scripture — but the human is always in control.

---

## Design Decisions

### Aesthetic
- Four selectable visual themes (set in Settings):
  - **Candlelight** (default): warm dark brown bg #0f0b05, gold accents #c8a44a, cream text
  - **Parchment**: warm light bg #f5edd8, dark brown accents — day/light mode
  - **Midnight**: deep blue-black bg #080c18, cool blue accents #7aaace
  - **Ember**: very dark warm bg #0c0503, orange-red accents #e07830
- All theme colours defined as CSS custom properties on [data-theme="..."] selectors on html element
- Theme persists in settings.theme
- Fonts: Playfair Display (headings) + Crimson Text (body) + Lato (UI labels)
- No gamification, no streaks, no badges

### Navigation
- Bottom nav: Home · Inbox · Prayers · Testimony · Wisdom
- Prayer detail is a sub-screen of Prayers (no separate nav tab)
- Evening Routine opens as a full-screen overlay
- Settings opens as a full-screen overlay (gear button top-right of Home screen)

### Organisational Model
- Themes (10 default): Work & Calling, Home & Living, Marriage, Family, Relationships, Church & Community, Health, Faith & Growth, Finances, Theology
- Each theme has a short description shown on its card
- Prayers belong to one or more themes (multi-tag, not siloed)
- Each prayer has an editable description. Editing it creates a new update entry so the change is tracked.
- Prayer statuses: active | watching | dormant | answered
- Entry types: request | update | observation | answer | principle | gratitude
- Each entry is tappable to open a full detail modal (date, time, scripture beautifully rendered)

### Prayer Tags / Edit
- "Edit" button in prayer detail header opens modal for: title, theme tags (multi-select), and status

### AI Layer
- Requires Anthropic API key (stored in localStorage)
- AI features: inbox processing, living summary generation, scripture suggestion, principle extraction
- All AI is assistive — user reviews before anything is filed
- Manual mode available for all features without API key

---

## Data Structure

All data stored in localStorage under key covenant_v1.

settings: { name, apiKey, routineCompletedDate (YYYY-MM-DD), tomorrowFocusPrayerId, theme }
themes: [{ id, name, icon, description }] — 10 defaults
prayers: [{ id, title, description, themeIds[], status, createdAt, answeredAt, entries[], livingSummary, linkedPrayerIds[] }]
  entries: [{ id, type (request|update|observation|answer|principle|gratitude), content, scripture, createdAt }]
inbox: [{ id, rawText, createdAt, processed }]
principles: [{ id, text, prayerIds[], createdAt }]
devNotes: [{ id, text, createdAt }]

---

## Screens

| Screen | How to reach | Description |
|---|---|---|
| Home | Nav: Home | Greeting, quick capture, routine card, focus card, testimony card |
| Inbox | Nav: Inbox | Unprocessed captures — file manually or with AI |
| Themes | Nav: Prayers | 10 themes with descriptions, tap to see prayers within |
| Prayer Detail | Tap any prayer | Description + threaded entries + living summary |
| Testimony | Nav: Testimony | Fully answered prayers + Along the Way milestones |
| Principles | Nav: Wisdom | Personal wisdom library |
| Evening Routine | Home routine card | 3-step guided overlay |
| Settings | Gear on Home | Profile, appearance, data backup, dev notes, future improvements |
| Setup | First launch | Name + optional API key |

---

## Key Flows

### Today's Focus (Home)
- "Today's Focus" card shows current focus prayer
- "Change prayer" link opens picker modal for any active prayer
- Auto-rotate option returns to round-robin by day-of-year
- Choice also settable in Step 3 of Evening Routine

### Prayer Filing
- Prayer picker in manual filing uses optgroup grouped by theme with status shown
- All entry types including gratitude available in all forms

### Entries
- Tappable to open full detail modal (type icon, date, time, scripture)
- Delete available from thread view and detail modal

### Dev Notes (Settings)
- Saved to db.devNotes[] — survives export/import
- Chris jots bugs/ideas here; shares with Claude by uploading backup JSON next session

### App Icon
- Generated at boot via Canvas API (candle with flame on dark background)
- Set as apple-touch-icon so Covenant appears correctly on home screen

---

## Evening Routine (3 steps)
1. Give Thanks — dynamic gratitude rows, each becomes [Gratitude] inbox capture
2. Inbox Review — shows unprocessed count
3. Pray Forward — shows current focus prayer, allows changing; surrender notes become [Surrender] captures
- Stamps routineCompletedDate, saves focus choice to tomorrowFocusPrayerId

---

## Mobile / Firefox Fix
- Nav uses padding-bottom: max(14px, env(safe-area-inset-bottom))
- Screen content uses padding-bottom: 130px (increased to clear Firefox URL bar)
- min-height: 100dvh (dynamic viewport height) on body

---

## Hosting

Single HTML file (index.html) on GitHub Pages.
Repo: https://github.com/Clarity-Lovewell/covenant
Live URL: https://clarity-lovewell.github.io/covenant/
Workflow: Edit locally -> commit via GitHub Desktop -> push to main -> live ~60 seconds

---

## CHANGELOG

| Date | Change |
|---|---|
| v1.0 — 2026-03-11 | Initial build: all core features |
| v1.1 — 2026-03-11 | Export/import backup |
| v1.1 — 2026-03-12 | Quick capture top; routine tracking; multiple notes; focus picker; prayer descriptions; theme descriptions; testimony milestones; 16 verses; PWA meta tags |
| v1.2 — 2026-03-12 | 10 prayer themes (added Family, Relationships, Church & Community, Theology); 4 visual themes (Candlelight, Parchment, Midnight, Ember) in Settings overlay; canvas app icon; Settings screen; Today's Focus rename + change picker; prayer edit modal (title/tags/status); gratitude entry type; grouped prayer picker; entry expansion modal with scripture; bug fix: ENTRY_ICONS restored; Firefox nav fix |

---

## Known Limitations

- No cross-device sync (data stays on one browser/device)
- No PDF or Markdown export (JSON backup only)
- Theme colours not user-customisable (4 presets only)
- No search across all prayers yet
- No prayer linking UI yet

---

## Planned v2 Features

- [ ] Search across all prayers, entries, principles
- [ ] Link prayers together
- [ ] Custom theme colours (user-defined palettes, day/night auto-switching)
- [ ] Export as PDF journal
- [ ] Scripture browser (search by keyword)
- [ ] Weekly review summary (AI-generated)
- [ ] Doctrine & Theology section (explain topics like Baptism, Grace, etc.)
- [ ] Shared/exported testimony (anonymised)
- [ ] Prayer groups (cross-category focused sessions)
- [ ] Rename, reorder, customise prayer categories

---

*Covenant SPEC v1.2 — Built with Claude Sonnet, March 2026*
