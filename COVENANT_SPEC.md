# Covenant — Prayer Journal · SPEC v1.7

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
- **Custom theme editor is only shown in Settings when Horizon or Dawn is the active theme** — no clutter for preset theme users
- Custom theme editor uses native `<input type="color">` swatches (directly clickable, render as coloured squares) alongside editable hex text fields — changes apply live with no "Save" button needed
- Custom theme colour math: surfaces derived by stepping bg brightness ±14 per level; cream/muted toggled by luminance threshold (< 0.5 = dark mode behaviour)
- Preset themes use `[data-theme="..."]` CSS selectors; custom themes inject inline CSS vars on the html element
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
- Prayer statuses: active | watching | dormant | answered
- Entry types: request | update | observation | answer | principle | gratitude
- Each entry is tappable to open a full detail modal (date, time, scripture beautifully rendered)

### Prayer Detail Header (redesigned)
- Stacked column layout:
  - Row 1: Back button + Prayer title (wraps naturally, full width)
  - Row 2: Status badge (with emoji) + Theme tags (wrapping flex row)
  - Row 3: ✎ Edit · ⊟ Filter · + Entry action buttons


### All Prayers View
- Accessible via "All Prayers" toggle on the Prayers screen (next to "By Theme")
- Toggle persists within the session (`_prayerView` state)
- **Filter strip**: All · 🟢 Active · 👁️ Watching · 💤 Dormant · ✅ Answered — with live counts
- **Sort buttons**: Recent activity (default) · A → Z · Oldest first
- Within each sort, primary grouping is always by status (active → watching → dormant → answered)
- Each row shows: status emoji, prayer title, theme tags (up to 2), time since last entry, last entry snippet (2 lines), entry count
- Answered prayers visually dimmed and title in green
- Tapping any row navigates directly to prayer detail
- Status groups shown as labelled headers with counts when "All" filter is active

### Prayer Tags / Edit
- "✎ Edit" button (gold ghost style) in prayer detail header opens modal for: title, theme tags (multi-select), and status
- Status badge in prayer detail is also directly tappable → opens quick status picker

### Entry Filtering
- "⊟ Filter" button (gold ghost style) in prayer detail header
- When active: button gains gold background tint (`.filter-btn-active`) to show state
- Opens inline filter bar with pill buttons for each entry type
- Active filters highlighted in gold; "✕ Clear" button to reset

### Add Entry — Bottom CTA
- A dashed "+ Add Entry" pill button at the bottom of the entry thread, in addition to the "+ Entry" button in the header

### Entry Editing
- Every entry shows an **Edit** link in the thread (alongside Delete)
- **✎ Edit Entry** button also in the entry detail modal
- Edit modal allows changing: type, content, scripture
- On save: `editedAt` timestamp recorded on the entry
- Thread shows "· edited [relative date]" label in the entry meta row
- Entry detail modal shows edited date in the timestamp line
- No full edit history — simple overwrite with timestamp. `originalRaw` (inbox capture) still preserved where applicable.

### Prayer Status Emojis
- Status displayed with emoji throughout the app: 🟢 Active · 👁️ Watching · 💤 Dormant · ✅ Answered

### Dismiss Inbox Items — Confirmation
- "Dismiss" button on inbox items shows a confirmation dialog before marking as processed

### Mark as Answered — Prompt on Answer Entry
- When an entry of type "answer" is filed (via Add Entry or Edit Entry), a modal appears asking whether to also mark the prayer as **Answered** and move it to Testimonies
- User chooses "Yes, it's answered ✅" or "Not yet"
- The `markAnswered()` button (in prayer detail footer) still marks directly as before since it's already intentional

### AI Layer
- Requires Anthropic API key (stored in localStorage)
- AI features:
  - **Inbox processing**: processes raw capture → shows before/after comparison with editable draft textarea
  - **Living summary**: summarises prayer thread in second person ("You began this prayer…")
  - **Scripture suggestion** (available in four places):
    - Inbox "File Manually" modal — "✦ Suggest Scripture" button below scripture field
    - Add Entry modal — "✦ Suggest Scripture" button below scripture field
    - Edit Entry modal — "✦ Suggest Scripture" button below scripture field
    - Entry Detail modal — "✦ Suggest Scripture" button shown when no scripture exists; result can be added directly to the saved entry via "Add to Entry" button (persists immediately)
  - **Principle extraction**: detected during inbox AI processing — shown with an **unchecked checkbox** asking "save to Wisdom?". User must explicitly tick to save. Not auto-saved.
  - **Prayer description generation**: "✦ AI Generate" in description area, Edit Description modal, and New Prayer modal
  - **Entry assist**: "✦ AI Assist" in Add Entry modal — polishes raw note, can suggest spinning off a new prayer
- All AI prompts are tuned for concise, natural, direct output — no religious filler or formulaic language
- All AI is assistive — user reviews/edits before anything is filed
- Manual mode available for all features without API key

### AI Prompt Style
- All AI prompts instruct for: concise (1-3 sentences), first person, natural and direct — no religious clichés or filler
- Entry drafts: 1-3 sentences
- Descriptions: 1 sentence
- Living summaries: 2-3 sentences
- Scripture suggestions: reference + brief quote only

### File Manually — Retains AI Context or Raw Capture
- "File Manually" button in the **AI suggestion panel**: pre-fills entry text from edited AI draft, type, prayer, and scripture from AI suggestion
- "File Manually" button on the **original inbox item** (before/after AI runs): pre-fills entry text from the raw capture only — AI suggestion data is ignored
- This lets the user always file from the raw original without AI contamination, even after AI has run

### Inbox — Before/After + Editable Draft
- When AI processes an inbox item, the suggestion panel shows:
  1. **Original capture** (read-only, italic, labelled "Original capture")
  2. A "↓ refined" arrow divider
  3. An **editable textarea** pre-filled with the AI draft — user can tweak before accepting
- On "Accept & File": the edited draft text is used as entry content
- The original raw capture is stored on the entry as `originalRaw`
- In the entry detail modal, if `originalRaw` exists: a "📎 View original capture" toggle reveals the original text

### Scripture Expansion
- Entries with a scripture reference show a "📖 Load passage" button in the detail modal
- Tapping it fetches the passage from bible-api.com (WEB translation) and displays it inline
- No API key required for scripture expansion

### Voice Capture
- 🎤 microphone button next to Capture button on the Home quick-capture area
- Uses Web Speech API (`SpeechRecognition` / `webkitSpeechRecognition`)
- On tap: starts recording, button turns red with pulse animation
- On result: appends transcribed text to the quick-capture textarea
- If API not supported (non-Chrome): shows brief "✗" state and an alert
- Language set to `en-AU`

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
}

themes: [{ id, name, icon, description }]

prayers: [{
  id, title, description, themeIds[], status,
  createdAt, answeredAt, entries[], livingSummary, linkedPrayerIds[]
}]

entries: [{
  id, type, content, scripture, createdAt,
  editedAt (optional — timestamp of last edit),
  originalRaw (optional — stores pre-AI raw capture for before/after view)
}]

inbox:      [{ id, rawText, createdAt, processed }]
principles: [{ id, text, prayerIds[], createdAt }]

devNotes: [{
  id, text, createdAt,
  checked (bool — ticked when tested),
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
| Testimony | Nav: Testimony | Fully answered prayers + Along the Way milestones |
| Principles | Nav: Wisdom | Personal wisdom library |
| Evening Routine | Home routine card | 3-step guided overlay |
| Settings | Gear on Home | Profile, appearance (incl. custom themes), data backup, dev notes |
| Setup | First launch | Name + optional API key |

---

## Key Flows

### Today's Focus (Home)
- "Today's Focus" card shows current focus prayer
- "Change prayer" link opens picker modal grouped by theme category
- Auto-rotate option returns to round-robin by day-of-year
- Choice also settable in Step 3 of Evening Routine

### Prayer Filing
- Prayer picker in manual filing uses optgroup grouped by theme with status emoji shown
- All entry types including gratitude available in all forms

### Entries
- Tappable to open full detail modal (type icon, date, time, scripture)
- Scripture reference shows "📖 Load passage" → fetches full passage from bible-api.com
- If entry has `originalRaw`: "📎 View original capture" toggle shown in detail modal
- Edit and Delete available from both thread view and detail modal
- Edited entries show "· edited [date]" label in thread and detail modal
- "+ Add Entry" button at both top (header) and bottom of thread

### Dev Notes (Settings)
- Saved to `db.devNotes[]` — survives export/import
- Each note has a **checkbox** to mark as tested/verified
- Ticking a note strikes through its text and records `checkedAt` timestamp
- Checked notes are grouped into a collapsible "✓ Show Archived (n)" section below active notes
- Unchecked notes can be edited inline; checked notes show "tested" date
- Notes are never auto-deleted — archive by ticking, remove manually with the Remove button

### App Icon
- Generated at boot via Canvas API (candle with flame on dark background)
- Used in apple-touch-icon and injected into a dynamically-created manifest Blob URL
- Manifest includes icons array so Chrome Android shows the "Add to Home Screen" install prompt

---

## Evening Routine (3 steps)
1. **Give Thanks** — dynamic gratitude rows, each becomes [Gratitude] inbox capture
2. **Inbox Review** — shows unprocessed count; "Review Inbox →" navigates to inbox **without closing or resetting the routine**. Re-opening the routine card shows the same state with inputs preserved.
3. **Pray Forward** — shows current focus prayer, allows changing; surrender notes become [Surrender] captures
- Stamps `routineCompletedDate`, saves focus choice to `tomorrowFocusPrayerId`
- Closing via ✕ or completing the routine resets state; next open starts fresh
- `_routineRendered` JS flag controls this: set `true` on open, cleared on ✕ close or complete

---

## PWA / Mobile

- **Manifest**: Dynamically generated as Blob URL at boot with proper icons array — Chrome Android shows "Add to Home Screen" install prompt
- **Service Worker**: Registered at boot via Blob URL — caches the app for offline use
- **Recommended install flow**: Open in Chrome on Android → tap ⋮ menu → "Add to Home Screen" → launches fullscreen, no browser chrome
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
- **For larger batches (3+ dev notes):** split into two prompts — first handles code-heavy changes (theming, new modals, data structure), second handles lighter changes (copy tweaks, confirmation dialogs, UI polish). This avoids hitting output length limits mid-spec.
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
| v1.2 | 2026-03-12 | 10 prayer themes; 4 visual themes (Candlelight, Parchment, Midnight, Ember); canvas app icon; Settings screen; Today's Focus + change picker; prayer edit modal; gratitude entry type; grouped prayer picker; entry expansion modal with scripture; ENTRY_ICONS fix; Firefox nav fix |
| v1.3 | 2026-03-12 | PWA: dynamic manifest + service worker; Cocoa theme; Focus picker grouped by theme; Entry filter bar; Scripture passage expansion (bible-api.com); Dev note editing inline; Status badge clickable; Edit button gold ghost style |
| v1.4 | 2026-03-13 | Custom themes Horizon + Dawn with live colour pickers; Prayer detail header redesign (stacked layout); Filter button gold ghost style with active highlight; Add Entry button at bottom of thread; Status emojis throughout; Prayer list cards with emoji status; AI description generation; AI entry assist with new-prayer suggestion; Inbox before/after comparison with editable draft; Original capture stored on entry with toggle; Dev notes checkbox — tick to archive, collapsible archived section |
| v1.5 | 2026-03-13 | Dismiss inbox confirmation dialog; Custom theme editor rebuilt — native colour inputs + hex text fields, live apply, only shown when Horizon/Dawn active; File Manually retains AI-suggested prayer/type/scripture; AI scripture suggestion in Add Entry, Edit Entry, Entry Detail (posted entries), and File Manually modals; Entry editing (type + content + scripture) with editedAt timestamp shown in thread and detail modal |
| v1.7 | 2026-03-14 | All Prayers view — flat sorted list with filter strip and sort toggles, accessible via toggle on Prayers screen |
| v1.6 | 2026-03-14 | AI prompts tightened — concise, natural, no filler; "Mark as Answered?" prompt when filing answer-type entries (submitEntry + submitEditEntry); AI-detected principles no longer auto-saved — shown with unchecked checkbox for user to opt in; Voice capture button (🎤) on home quick-capture using Web Speech API; "File Manually" from original inbox item uses raw capture text (ignores AI data); Evening routine state preserved when navigating to inbox mid-routine via `_routineRendered` flag |

---

## Known Limitations

- No cross-device sync (data stays on one browser/device)
- No PDF or Markdown export (JSON backup only)
- No search across all prayers yet
- No prayer linking UI yet
- Service worker Blob URL may not persist after page close on some browsers; full offline requires deploying a sw.js file alongside index.html
- Voice capture (Web Speech API) works in Chrome only; not supported in Firefox or Safari

---

## Planned v2 Features

- [ ] Search across all prayers, entries, principles
- [ ] Link prayers together
- [ ] Export as PDF journal
- [ ] Scripture browser (search by keyword)
- [ ] Weekly review summary (AI-generated)
- [ ] Doctrine & Theology section
- [ ] Shared/exported testimony (anonymised)
- [ ] Prayer groups (cross-category focused sessions)
- [ ] Rename, reorder, customise prayer categories

---

*Covenant SPEC v1.7 — Built with Claude Sonnet, March 2026*
