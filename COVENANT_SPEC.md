# Covenant — Prayer Journal · SPEC v1.1

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
- Warm, dark, candlelight tone — feels like a leather prayer journal, not a productivity app
- Fonts: Playfair Display (headings, serif) + Crimson Text (body, readable serif) + Lato (UI labels, sans)
- Colour palette: deep warm brown background (#0f0b05), gold accents (#c8a44a), warm cream text (#efe5c5)
- No gamification, no streaks, no badges

### Navigation
- Bottom nav: Home · Inbox · Prayers · Testimony · Wisdom
- Prayer detail is a sub-screen of Prayers (no separate nav tab)
- Evening Routine opens as a full-screen overlay

### Organisational Model
- **Themes** (6 default): Work & Calling, Home & Living, Marriage, Health, Faith & Growth, Finances
- Each theme has a short **description** shown on its card (e.g. "Prayers about vocation, purpose, and daily work.")
- **Prayers** belong to one or more themes (multi-tag, not siloed)
- Each prayer has an editable **description** — a short note about what is being prayed for and where things stand. Editing it creates a new `update` entry in the thread so the change is tracked over time.
- **Prayers** have statuses: `active` | `watching` | `dormant` | `answered`
- **Entries** within each prayer are threaded chronologically (like a conversation)
- Entry types: `request` | `update` | `observation` | `answer` | `principle`

### AI Layer
- Requires Anthropic API key (stored in localStorage on user's device)
- AI features: inbox processing, living summary generation, scripture suggestion, principle extraction
- All AI is assistive — user reviews and accepts/edits before anything is filed
- Works without API key (manual mode available for all features)

---

## Data Structure

All data stored in `localStorage` under key `covenant_v1`.

```json
{
  "settings": {
    "name": "string",
    "apiKey": "string (stored locally, never transmitted except to Anthropic)",
    "routineCompletedDate": "YYYY-MM-DD — date the evening routine was last completed",
    "tomorrowFocusPrayerId": "prayer id — user's chosen focus prayer, or empty string for auto-rotation"
  },
  "themes": [
    {
      "id": "work",
      "name": "Work & Calling",
      "icon": "💼",
      "description": "Prayers about vocation, purpose, and daily work."
    }
  ],
  "prayers": [
    {
      "id": "uuid",
      "title": "Job & Career",
      "description": "A short note about what is being prayed for and where things stand.",
      "themeIds": ["work", "home"],
      "status": "active",
      "createdAt": 1234567890,
      "answeredAt": null,
      "entries": [
        {
          "id": "uuid",
          "type": "request | update | observation | answer | principle",
          "content": "string",
          "scripture": "string",
          "createdAt": 1234567890
        }
      ],
      "livingSummary": "AI-maintained paragraph summary",
      "linkedPrayerIds": ["uuid"]
    }
  ],
  "inbox": [
    {
      "id": "uuid",
      "rawText": "string",
      "createdAt": 1234567890,
      "processed": false
    }
  ],
  "principles": [
    {
      "id": "uuid",
      "text": "string",
      "prayerIds": ["uuid"],
      "createdAt": 1234567890
    }
  ]
}
```

---

## Screens

| Screen | How to reach | Description |
|---|---|---|
| Home | Nav: Home | Greeting, quick capture (top), evening routine, at-a-glance cards |
| Inbox | Nav: Inbox | Unprocessed captures — file manually or with AI |
| Themes | Nav: Prayers | List of all 6 themes with descriptions, tap to see prayers within |
| Prayer Detail | Tap any prayer | Description + threaded entry view + living summary |
| Testimony | Nav: Testimony | Fully answered prayers + "Along the Way" milestones section |
| Principles | Nav: Wisdom | Personal wisdom library |
| Evening Routine | Home → "Begin tonight's practice" | 3-step guided practice (fullscreen overlay) |
| Setup | First launch | Name + optional API key |

---

## Key Flows

### Quick Capture
- Always visible at the **top** of the Home screen
- Type anything — no decisions needed
- Saves instantly to Inbox as unprocessed

### Inbox Processing (AI)
1. User taps "Process with AI" on inbox item
2. AI reads raw text, suggests: entry draft, prayer to file to (or new prayer), scripture, entry type, and any principle
3. User reviews AI suggestion
4. User taps "Accept & File" — entry is added to the prayer, principle saved if detected
5. Inbox item marked processed

### Evening Routine (3 steps)
1. **Give Thanks** — one or more gratitude textareas (dynamic "Add another" rows). Each non-empty note becomes a separate `[Gratitude]` inbox capture.
2. **Inbox Review** — shows unprocessed count; button to go process inbox
3. **Pray Forward** — shows tomorrow's focus prayer. User can tap "⇄ Choose different prayer" to pick from all active prayers. One or more surrender notes (dynamic rows), each becoming a `[Surrender]` inbox capture.
- Completing the routine saves the chosen focus prayer and stamps today's date as `routineCompletedDate`
- Home screen shows "✓ Done today" on the routine card if already completed today
- Routine can still be opened and completed again; it just notes that it's already been done

### Living Summary
- AI-generated paragraph at top of each prayer
- Shows the full arc: when it started, how it evolved, current status, key principles
- Manually refreshable by tapping "↻ Refresh"

### Focus Prayer Logic
- Default: round-robin through active prayers by day of year
- Override: user picks a specific prayer in Step 3 of the Evening Routine
- The chosen prayer persists in `settings.tomorrowFocusPrayerId` until changed
- Home screen "Tonight's Focus" card respects the chosen prayer

### Testimony
- **Fully answered** section: prayers with status `answered`, most recent first
- **Along the Way** section: active or watching prayers that contain at least one `answer` type entry — shown with a blue border to distinguish from completed testimonies

---

## AI Prompts (v1)

### Inbox Processing Prompt
Asks for JSON with: `summary`, `entryDraft`, `suggestedPrayer`, `suggestedTheme`, `entryType`, `scripture`, `principle`

### Living Summary Prompt
Asks for 3–5 sentence summary in second person ("You started..."), warm and faith-affirming

---

## Hosting

**Current:** Single HTML file (`index.html`) hosted on GitHub Pages.
Repo: https://github.com/Clarity-Lovewell/covenant
Live URL: https://clarity-lovewell.github.io/covenant/

**Workflow:** Edit locally → commit via GitHub Desktop → push to main → live within ~60 seconds.

**PWA / Mobile:** App includes meta tags for fullscreen mobile experience. Add to iPhone home screen via Safari Share → "Add to Home Screen". Re-add after any update that changes the meta tags.

---

## CHANGELOG

| Date | Change | Who |
|---|---|---|
| v1.0 — 2026-03-11 | Initial build: all core features | Claude |
| v1.1 — 2026-03-11 | Export/import backup feature | Claude |
| v1.1 — 2026-03-12 | Quick capture moved to top of Home; evening routine completion tracking + multiple notes + focus prayer picker; prayer descriptions (editable, creates update entry); theme descriptions; testimony milestones section; scripture verses completed and expanded to 16; PWA fullscreen meta tags; safe area insets for iPhone notch | Claude |

---

## Known Limitations

- No cross-device sync (data stays on one browser/device)
- No PDF or Markdown export (JSON backup only)
- Themes are fixed — names and icons cannot be customised yet (planned v2)
- No search across all prayers yet (planned v2)
- No prayer linking UI yet (planned v2)

---

## Planned v2 Features

- [ ] Search across all prayers, entries, principles
- [ ] Link prayers together (e.g. Job + House prayers)
- [ ] Custom themes (edit name, icon, description)
- [ ] Export as PDF journal
- [ ] Scripture browser (search by keyword)
- [ ] Settings screen (change name, API key, manage data)
- [ ] Weekly review summary (AI-generated)
- [ ] Shared/exported testimony (anonymised, for sharing with others)
- [ ] Prayer categories — deeper work (display, ordering, custom grouping)

---

*Covenant SPEC v1.1 — Built with Claude Sonnet, March 2026*
