# Covenant — Prayer Journal · SPEC v1.9

> **This document is the single source of truth for the Covenant app.**
> Upload this file alongside index.html (and optionally a JSON backup) at the start of every Claude session.
> Claude will update this file directly — you do not need to edit it manually.

---

## Purpose

A personal prayer and faith journal that goes beyond tracking prayer requests — it captures God's faithfulness over time, extracts personal wisdom from lived experience, and builds a searchable spiritual record tied to Scripture.

The core difference from existing apps: prayers are *living threads* that evolve over time, not static list items. AI assists with processing raw thoughts, drafting journal entries, generating living summaries, and suggesting scripture — but the human is always in control.

---

## Design Decisions

### Aesthetic
- Seven selectable visual themes (set in Settings):
  - **Candlelight** (default): warm dark brown bg #0f0b05, gold accents #c8a44a, cream text
  - **Parchment**: warm light bg #f5edd8, dark brown accents — day/light mode
  - **Midnight**: deep blue-black bg #080c18, cool blue accents #7aaace
  - **Ember**: very dark warm bg #0c0503, orange-red accents #e07830
  - **Cocoa**: chocolate brown bg #1a0e08, warm tan accents #d4a060, ivory text
  - **Horizon** (custom dark): user-defined bg + accent — defaults to dark navy + sky blue
  - **Dawn** (custom light): user-defined bg + accent — defaults to warm white + violet
- Horizon and Dawn are fully user-customisable: background colour + accent colour
- **Custom theme editor is only shown in Settings when Horizon or Dawn is the active theme**
- Custom theme editor uses native `<input type="color">` swatches + editable hex text fields — changes apply live
- Preset themes use `[data-theme="..."]` CSS selectors; custom themes inject inline CSS vars
- Theme persists in `settings.theme`; custom colours in `settings.customTheme1` / `settings.customTheme2`
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
- Each prayer has an editable description. Editing it creates a new update entry so the change is tracked. AI can generate a description from prayer title + recent entries.
- Prayer statuses: active | watching | dormant | ongoing | answered
- Entry types: request | update | observation | answer | principle | gratitude | question | todo
- Each entry is tappable to open a full detail modal (date, time, scripture beautifully rendered)

### Prayer Status
- **Active** 🟢 — currently praying
- **Watching** 👁️ — waiting on God's timing
- **Ongoing** 🔄 — a long-running prayer with multiple answers over time (appears in Testimony milestones alongside answered prayers)
- **Dormant** 💤 — paused for now
- **Answered** ✅ — God has fully answered this prayer; moves to Testimonies

### Entry Types
- **request** 🙏 — a prayer request
- **update** 📝 — a progress note
- **observation** 👁 — something noticed or reflected on
- **gratitude** 💛 — a moment of thanks
- **answer** ✨ — an answered prayer moment (can mark prayer as Answered)
- **principle** 📖 — a wisdom lesson extracted from the experience
- **question** ❓ — a theological or spiritual question to sit with
- **todo** ☐ — a practical action item arising from prayer

### Prayer Detail Header (redesigned)
- Stacked column layout:
  - Row 1: Back button + Prayer title (wraps naturally, full width)
  - Row 2: Status badge (with emoji) + Theme tags (wrapping flex row)
  - Row 3: ✎ Edit · ⊟ Filter · + Entry action buttons

### All Prayers View
- Accessible via "All Prayers" toggle on the Prayers screen
- **Filter strip**: All · 🟢 Active · 👁️ Watching · 💤 Dormant · 🔄 Ongoing · ✅ Answered — with live counts
- **Sort buttons**: Recent activity (default) · A → Z · Oldest first
- Within each sort, primary grouping is always by status order
- Each row shows: status emoji, prayer title, theme tags (up to 2), time since last entry, last entry snippet, entry count
- Ongoing prayers shown with a blue left border accent

### Inbox Categories
- Every inbox item has a `category` field: `capture` (default) | `prayer` | `todo` | `gratitude`
- **Home quick capture box** shows 3 small category pills below the textarea: ⚡ Capture · 🙏 Prayer · ☐ To-Do — tap to select before capturing; defaults back to Capture after each submission
- **Inbox screen** shows a filter strip at the top: All · ⚡ Capture · 🙏 Prayer · ☐ To-Do · 💛 Gratitude — with live counts per category; filters the inbox list without affecting processing
- Each inbox item shows its category badge in the top-right of its card
- **Evening Routine**: Gratitude rows auto-tagged `gratitude`; Surrender rows auto-tagged `prayer` when filed to inbox
- Category is stored on the inbox item and visible until the item is processed

### Compact Thread UI (Prayer Detail)
- Entries display as **compact message bubbles** — similar to a messaging app thread
- Each bubble has a **coloured left border** indicating entry type (gold = request, blue = update/question, green = answer, purple = principle, pink = gratitude, muted = observation/todo)
- Entry header: type label (icon + name) on the left, date/time on the right — small Lato caps
- Entry content: Crimson Text 15px, tight 1.5 line-height
- Scripture (when present): compact italic block with gold left border, 13px, below content
- Entry actions (Edit · Delete · edited note): subtle, below content, stop propagation from bubble click
- Tapping anywhere on the bubble opens the full entry detail modal
- No thread line, no dots — clean vertical list of cards


- Search input filters prayers live as you type
- Results grouped by theme with sticky headers, scrollable to max 220px height
- "Create New Prayer" option always shown at the top
- Selected prayer highlighted in gold; tapping a row selects it and shows/hides the new prayer fields accordingly
- Replaces the old `<optgroup>` select for much easier navigation when prayer list is long

### Entry Filtering
- "⊟ Filter" button in prayer detail header
- When active: button gains gold background tint to show state
- Opens inline filter bar with pill buttons for each entry type (now includes question + todo)
- Active filters highlighted in gold; "✕ Clear" button to reset

### Add Entry — Bottom CTA
- A dashed "+ Add Entry" pill button at the bottom of the entry thread

### Entry Editing
- Every entry shows an **Edit** link in the thread (alongside Delete)
- **✎ Edit Entry** button also in the entry detail modal
- Edit modal allows changing: type, content, scripture
- On save: `editedAt` timestamp recorded on the entry

### Prayer Status Emojis
- Status displayed with emoji throughout: 🟢 Active · 👁️ Watching · 💤 Dormant · 🔄 Ongoing · ✅ Answered

### Testimony Screen
- Fully answered prayers shown as testimony cards
- **Ongoing prayers** now also appear in the "Along the Way" milestones section (alongside prayers with answer-type entries)
- Ongoing prayers show a 🔄 icon and a count of answers recorded so far

### Dismiss Inbox Items — Confirmation
- "Dismiss" button on inbox items shows a confirmation dialog before marking as processed

### Mark as Answered — Prompt on Answer Entry
- When an entry of type "answer" is filed (via Add Entry or Edit Entry), a modal asks whether to also mark the prayer as **Answered**
- User chooses "Yes, it's answered ✅" or "Not yet"

### Voice Capture (rewritten for Android Chrome reliability)
- 🎤 microphone button next to Capture button on the Home quick-capture area
- Uses Web Speech API (`SpeechRecognition` / `webkitSpeechRecognition`)
- **Android Chrome fix**: `continuous: false` + auto-restart on `onend` to simulate continuous mode
- Live interim transcription shown in textarea as user speaks
- Final transcripts accumulated across restart cycles in `_voiceAccumulated`
- Original textarea value preserved in `_voiceOriginalValue` — appended to on commit
- Tap ⏹ to stop: accumulated text committed to textarea
- No separate "Listening…" status label — button state (red pulse) is the indicator
- On genuine errors (not `no-speech` / `aborted`): stops gracefully and commits whatever was captured

### AI Layer
- Requires Anthropic API key (stored in localStorage)
- AI features:
  - **Inbox processing**: processes raw capture → shows before/after comparison with editable draft textarea; AI now also suggests `question` and `todo` entry types
  - **Living summary**: summarises prayer thread in second person ("You began this prayer…")
  - **Scripture suggestion** (available in four places): Add Entry, Edit Entry, Entry Detail, File Manually modals
  - **Principle extraction**: shown with an **unchecked checkbox** — user must explicitly tick to save
  - **Prayer description generation**: "✦ AI Generate" in description area, Edit Description modal, and New Prayer modal
  - **Entry assist**: "✦ AI Assist" in Add Entry modal — polishes raw note, can suggest spinning off a new prayer
- All AI prompts tuned for concise, natural, direct output — no religious filler
- All AI is assistive — user reviews/edits before anything is filed

### File Manually — Retains AI Context or Raw Capture
- "File Manually" from the **AI suggestion panel**: pre-fills from edited AI draft, type, prayer, and scripture
- "File Manually" from the **original inbox item** (before AI runs): pre-fills from raw capture only

### Dev Notes (Settings)
- Saved to `db.devNotes[]` — survives export/import
- Each note has a **checkbox** to mark as tested/verified
- Ticking a note strikes through its text and records `checkedAt` timestamp
- Checked notes are grouped into a collapsible "✓ Show Archived (n)" section
- Unchecked notes can be edited inline; checked notes show "tested" date
- Notes are never auto-deleted — archive by ticking, remove manually

---

## Data Structure

All data stored in localStorage under key `covenant_v1`.

```
settings: {
  name,
  apiKey,
  routineCompletedDate (YYYY-MM-DD),
  tomorrowFocusPrayerId,
  theme,
  customTheme1: { bg: hex, accent: hex },
  customTheme2: { bg: hex, accent: hex },
  routineDraft: { date, thanks[], surrender[] } | null,
}

themes: [{ id, name, icon, description }]

prayers: [{
  id, title, description, themeIds[], status,
  createdAt, answeredAt, entries[], livingSummary, linkedPrayerIds[]
}]

entries: [{
  id,
  type,       // request | update | observation | answer | principle | gratitude | question | todo
  content, scripture, createdAt,
  editedAt (optional),
  originalRaw (optional — stores pre-AI raw capture)
}]

inbox:      [{ id, rawText, createdAt, processed }]
principles: [{ id, text, prayerIds[], createdAt }]

devNotes: [{
  id, text, createdAt,
  checked (bool),
  checkedAt (timestamp or null)
}]
```

---

## Screens

| Screen | How to reach | Description |
|---|---|---|
| Home | Nav: Home | Greeting, quick capture (with voice button), routine card, focus card, testimony card |
| Inbox | Nav: Inbox | Unprocessed captures — file manually or with AI |
| Prayers | Nav: Prayers | Toggle between "By Theme" (grid of 10 themes) and "All Prayers" (flat sorted list) |
| Prayer Detail | Tap any prayer | Description + threaded entries + living summary |
| Testimony | Nav: Testimony | Fully answered prayers + Ongoing prayers + Along the Way milestones |
| Principles | Nav: Wisdom | Personal wisdom library |
| Evening Routine | Home routine card | 3-step guided overlay |
| Settings | Gear on Home | Profile, appearance (incl. custom themes), data backup, dev notes |
| Setup | First launch | Name + optional API key |

---

## Key Flows

### Today's Focus (Home)
- "Today's Focus" card shows current focus prayer (only active prayers)
- "Change prayer" link opens picker modal grouped by theme category
- Auto-rotate option returns to round-robin by day-of-year

### Prayer Filing — Searchable Picker
- File Manually modal shows an inline searchable prayer list (not a select dropdown)
- Search input filters by title; results grouped by theme with sticky headers
- Selecting a prayer highlights it gold and hides/shows the new prayer fields

### Entries
- Tappable to open full detail modal (type icon, date, time, scripture)
- Scripture reference shows "📖 Load passage" → fetches full passage from bible-api.com
- If entry has `originalRaw`: "📎 View original capture" toggle shown in detail modal
- Edit and Delete available from both thread view and detail modal
- "+ Add Entry" button at both top (header) and bottom of thread
- Filter bar now includes question and todo types

### Testimony — Ongoing Prayers
- Ongoing prayers (status = 'ongoing') appear in the "Along the Way" milestones section with a 🔄 icon
- This section also shows active prayers that already have answer-type entries (partial answers)
- Both groups link back to the prayer detail

### Dev Notes (Settings)
- Each note has a checkbox to mark as tested/verified; ticking archives it
- Collapsible "✓ Show Archived" section below active notes

---

## Evening Routine (3 steps)
1. **Give Thanks** — dynamic gratitude rows, each becomes [Gratitude] inbox capture
2. **Inbox Review** — shows unprocessed count; "Review Inbox →" navigates to inbox without closing the routine
3. **Pray Forward** — shows current focus prayer, allows changing; surrender notes become [Surrender] captures
- Stamps `routineCompletedDate`, saves focus choice to `tomorrowFocusPrayerId`
- Draft preserved in `settings.routineDraft` when navigating away mid-routine
- Next-day draft prompts user to keep or discard

---

## Voice Capture (Technical Detail)
- `_voiceCapturing`: bool — controls whether auto-restart continues
- `_voiceAccumulated`: string — running total of final transcripts across restart cycles
- `_voiceOriginalValue`: string — textarea value at start of session; appended to on commit
- `continuous: false` with `onend` auto-restart: required for Android Chrome
- `interimResults: true`: live preview shown in textarea while speaking
- On stop tap: `_voiceCapturing = false` → next `onend` commits text instead of restarting
- No `getUserMedia` call needed (Web Speech API handles permissions internally)

---

## PWA / Mobile

- **Manifest**: Dynamically generated as Blob URL at boot with proper icons array
- **Service Worker**: Registered at boot via Blob URL — caches the app for offline use
- **Recommended install flow**: Open in Chrome on Android → tap ⋮ menu → "Add to Home Screen"
- Nav uses `padding-bottom: max(14px, env(safe-area-inset-bottom))`
- Screen content uses `padding-bottom: 130px`
- `min-height: 100dvh` on body

---

## Hosting

Single HTML file (`index.html`) on GitHub Pages.
Repo: https://github.com/Clarity-Lovewell/covenant
Live URL: https://clarity-lovewell.github.io/covenant/
Workflow: Edit → commit via GitHub Desktop or GitHub Mobile → push to main → live ~60 seconds

---

## Session Workflow

- Chris provides: `index.html` + `COVENANT_SPEC.md` + optional JSON backup at the start of each session
- **For small changes (1–2 features):** single prompt is fine
- **For larger batches (3+ dev notes):** split into two prompts — first handles code-heavy changes, second handles lighter changes (copy, UI polish, confirmation dialogs)
- Changes requested in plain language; Claude handles the code
- Claude delivers: updated `index.html` + updated `COVENANT_SPEC.md`
- Chris commits both files to GitHub

---

## CHANGELOG

| Version | Date | Change |
|---|---|---|
| v1.0 | 2026-03-11 | Initial build: all core features |
| v1.1 | 2026-03-11 | Export/import backup |
| v1.1 | 2026-03-12 | Quick capture top; routine tracking; multiple notes; focus picker; prayer descriptions; theme descriptions; testimony milestones; 16 verses; PWA meta tags |
| v1.2 | 2026-03-12 | 10 prayer themes; 4 visual themes; canvas app icon; Settings screen; Today's Focus + change picker; prayer edit modal; gratitude entry type; grouped prayer picker; entry expansion modal with scripture; ENTRY_ICONS fix; Firefox nav fix |
| v1.3 | 2026-03-12 | PWA: dynamic manifest + service worker; Cocoa theme; Focus picker grouped by theme; Entry filter bar; Scripture passage expansion; Dev note editing inline; Status badge clickable; Edit button gold ghost style |
| v1.4 | 2026-03-13 | Custom themes Horizon + Dawn; Prayer detail header redesign; Filter button active highlight; Add Entry button at bottom of thread; Status emojis; Prayer list cards; AI description generation; AI entry assist; Inbox before/after comparison; Original capture stored; Dev notes checkbox archive |
| v1.5 | 2026-03-13 | Dismiss inbox confirmation; Custom theme editor rebuilt; File Manually retains AI context; AI scripture suggestion in 4 places; Entry editing with editedAt timestamp |
| v1.6 | 2026-03-14 | AI prompts tightened; "Mark as Answered?" prompt on answer entries; Principle checkbox opt-in; Voice capture button; File Manually raw/AI distinction; Routine draft preserved during inbox navigation |
| v1.7 | 2026-03-14 | All Prayers view — flat sorted list with filter strip and sort toggles |
| v1.9 | 2026-04-11 | **Inbox categories** — Quick Capture, 🙏 Prayer, ☐ To-Do, 💛 Gratitude; category pills on home capture box; filter strip on Inbox screen; Routine auto-tags gratitude/surrender items; **Compact thread UI** — replaced dot/line thread with compact message bubbles; color-coded left border per entry type; tighter padding and typography; removed "Tap to expand" hint (tap anywhere on bubble opens detail) |

---

## Known Limitations

- No cross-device sync (data stays on one browser/device)
- No PDF or Markdown export (JSON backup only)
- No search across all prayers yet
- No prayer linking UI yet
- Service worker Blob URL may not persist after page close on some browsers
- Voice capture (Web Speech API) works in Chrome only; not supported in Firefox or Safari

---

## Planned Features

- [ ] Search across all prayers, entries, principles
- [ ] Link prayers together
- [ ] Export as PDF journal
- [ ] Scripture browser (search by keyword)
- [ ] Weekly review summary (AI-generated)
- [ ] Doctrine & Theology section
- [ ] Shared/exported testimony (anonymised)
- [ ] Rename, reorder, customise prayer categories

---

*Covenant SPEC v1.9 — Built with Claude Sonnet, April 2026*
