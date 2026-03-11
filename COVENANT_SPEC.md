# Covenant — Prayer Journal · SPEC v1.0

> **This document is the single source of truth for the Covenant app.**
> Any time you return to Claude to make changes, paste this document into the conversation.
> When you make your own edits to the HTML file, add a note to the CHANGELOG section below.

---

## Purpose

A personal prayer and faith journal that goes beyond tracking prayer requests — it captures God's faithfulness over time, extracts personal wisdom from lived experience, and builds a searchable spiritual record tied to Scripture.

The core difference from existing apps: prayers are *living threads* that evolve over time, not static list items. AI assists with processing raw thoughts, drafting journal entries, generating living summaries, and suggesting scripture — but the human is always in control.

---

## Design Decisions

### Aesthetic
- Warm, dark, candlelight tone — feels like a leather prayer journal, not a productivity app
- Fonts: Playfair Display (headings, serif) + Crimson Text (body, readable serif) + Lato (UI labels, sans)
- Colour palette: deep warm brown background (#120d06), gold accents (#c9a84c), warm cream text (#f0e6c8)
- No gamification, no streaks, no badges

### Navigation
- Bottom nav: Home · Inbox · Prayers · Testimony · Wisdom
- Prayer detail is a sub-screen of Prayers (no separate nav tab)
- Evening Routine opens as a full-screen overlay

### Organisational Model
- **Themes** (6 default): Work & Calling, Home & Living, Marriage, Health, Faith & Growth, Finances
- **Prayers** belong to one or more themes (multi-tag, not siloed)
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

All data stored in `localStorage` under key `covenant_data`.

```json
{
  "settings": {
    "name": "string",
    "apiKey": "string (stored locally, never transmitted except to Anthropic)"
  },
  "themes": [
    {
      "id": "work",
      "name": "Work & Calling",
      "icon": "💼",
      "color": "#c9a84c"
    }
  ],
  "prayers": [
    {
      "id": "uuid",
      "title": "Job & Career",
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
| Home | Nav: Home | Greeting, quick capture, evening routine, at-a-glance cards |
| Inbox | Nav: Inbox | Unprocessed captures — file manually or with AI |
| Themes | Nav: Prayers | Grid of all 6 themes, tap to see prayers within |
| Prayer Detail | Tap any prayer | Threaded entry view + living summary |
| Testimony | Nav: Testimony | All answered prayers in chronological order |
| Principles | Nav: Wisdom | Personal wisdom library |
| Evening Routine | Home → "Begin tonight's practice" | 3-step guided practice (fullscreen overlay) |
| Setup | First launch | Name + optional API key |

---

## Key Flows

### Quick Capture
- Always visible on Home screen
- Type anything — no decisions needed
- Saves instantly to Inbox as unprocessed

### Inbox Processing (AI)
1. User taps "Process with AI" on inbox item
2. AI reads raw text, suggests: entry draft, prayer to file to (or new prayer), scripture, entry type, and any principle
3. User reviews AI suggestion
4. User taps "Accept & File" — entry is added to the prayer, principle saved if detected
5. Inbox item marked processed

### Evening Routine (3 steps)
1. **Give Thanks** — textarea: what did God do today?
2. **Inbox Review** — shows count; button to go process inbox
3. **Pray Forward** — shows tomorrow's focus prayer; surrender textarea
- Completing the routine sends gratitude/surrender to inbox as unprocessed captures

### Living Summary
- AI-generated paragraph at top of each prayer
- Shows the full arc: when it started, how it evolved, current status, key principles
- Manually refreshable by tapping "↻ Refresh"

---

## AI Prompts (v1)

### Inbox Processing Prompt
Asks for JSON with: `summary`, `entryDraft`, `suggestedPrayer`, `suggestedTheme`, `entryType`, `scripture`, `principle`

### Living Summary Prompt
Asks for 3–5 sentence summary in second person ("You started..."), warm and faith-affirming

---

## Hosting

**Current:** Single HTML file, open in browser. All data saved locally via localStorage.

**Future option:** GitHub Pages (free, accessible from any device).
To migrate: Create GitHub repo → upload HTML file → enable Pages → access via private URL.

---

## CHANGELOG

> Add a line here whenever you make changes yourself (outside of Claude conversations).

| Date | Change | Who |
|---|---|---|
| v1.0 — initial build | All features above | Claude |

---

## Known Limitations (v1)

- No cross-device sync (data stays on one browser/device)
- No export yet (planned: export to PDF or Markdown)
- Themes are fixed (custom themes not yet supported)
- No search across all prayers yet (planned v2)
- No prayer linking UI yet (planned v2 — manual link between related prayers)

---

## Planned v2 Features

- [ ] Search across all prayers, entries, principles
- [ ] Link prayers together (e.g. Job + House prayers)
- [ ] Custom themes
- [ ] Export as PDF journal
- [ ] Scripture browser (search by keyword)
- [ ] Settings screen (change name, API key, manage data)
- [ ] Weekly review summary (AI-generated)
- [ ] Shared/exported testimony (anonymised, for sharing with others)

---

*Covenant SPEC v1.0 — Built with Claude Sonnet, March 2026*
