# Changelog

## 1.0.0

### Dashboard redesign

- Rebuilt the dashboard UI: a sticky header with segmented page tabs, search (press `/`), a theme picker, and a clickable tracking chip that pauses or resumes tracking.
- Added six dashboard themes (Pastel Dreamland, Midnight Nebula, Aurora Mint, Ember Sunset, Daylight, and Match VS Code). Pick one from the palette menu or the new `celestiaTime.dashboard.theme` setting; canvas charts follow the theme too.
- Replaced every filter and menu with one accessible dropdown component: keyboard navigation (arrows, Home/End, Enter, Escape), type-to-filter search for long project and language lists, multi-select checkboxes for activities, and automatic flipping when there is no room below.
- Redesigned language icons as brand-tinted tiles built from single-color Devicon marks, removing the white backing plate and keeping logos legible on dark and light themes.
- Date presets now apply immediately and highlight the active range; custom ranges still use Apply.
- Added an Export menu (JSON or CSV without an extra prompt), a More menu (data folder, extension settings, reset), a reload button, a rows-per-page selector, and a Clear button that shows how many filters are active.
- Clicking a language chip, project, legend row, or ranking row filters the dashboard to it.
- Added a goal line to the daily chart, a hover guide, a Busiest weekday KPI, a Peak hour insight, longest-streak detail, a week-aligned momentum map with month and weekday labels, and a Weekly pattern card.
- The dashboard remembers the selected page, filters, date range, time format, and page size, and applies setting changes (theme, time format, goal) without reopening.

### Performance

- The dashboard client is split into modules and does all filtering and aggregation in one memoized pass instead of many separate passes per render.
- Only the visible page renders; other pages re-render when opened. Charts redraw only when data, theme, or chart width changes.
- Icons are built once and cloned, replacing full-document `createIcons()` scans on every render and tooltip hover.
- Dropdown menus build their rows only when opened, and project/language menus rebuild only when the set of values changes.
- The heatmap uses one delegated listener instead of three listeners per cell; Intl formatters are cached.
- Removed per-card backdrop blur, which was expensive to composite.
- The context resolver caches exclusion checks, project and file IDs, and Git repository lookups per document instead of recomputing glob matches and SHA-256 hashes on every keystroke, scroll, and selection event.
- Appends to the day file reuse an in-memory copy (validated against size and mtime so other VS Code windows are respected) instead of re-reading the whole file on each flush.
- Dashboard history loads list the directory once and read existing day files in parallel.
- The snapshot timer runs only while the dashboard is visible, and live records are merged the same way the store merges them on disk.

### Fixes

- The today-total refresh is serialized with writes so an append finishing mid-read can no longer be counted twice.
- Appending to a day file that lacks a trailing newline no longer joins two records on one line.
- The dashboard keeps working across midnight instead of freezing "today" at the moment it opened.

## 0.9.5

- Recolored the dashboard with the Pastel Dreamland Adventure lavender, pink, and blue palette across controls, activity states, charts, heatmaps, cards, and navigation.
- Replaced near-black dashboard surfaces with layered violet-charcoal backgrounds and translucent panels that blend with the pastel theme.

## 0.9.4

- Stopped treating an activated Codex extension as proof that its sidebar is visible, which prevented closed sidebars and unrelated webviews from being recorded as Codex activity.
- Ended Codex when its editor tab is left or closed, and ended OpenCode when its terminal closes, switches away, or finishes a shell execution. Disabling AI-tool tracking also closes an active AI segment.

## 0.9.3

- Simplified activity-row headings to "Activity" and always placed the activity date and time directly beneath the heading.

## 0.9.2

- Added Codex view and OpenCode terminal detection as separate activity and language categories, including dashboard icons and an opt-out setting.
- Emitted the dashboard bundle with ASCII-safe Unicode escapes so punctuation renders correctly instead of appearing as mojibake in the VS Code webview.

## 0.9.1

- Fixed the Filtered time and Today cards showing mismatched duration formats by sharing one computation and one formatter for both.
- Cached compiled exclusion patterns so glob matching no longer rebuilds regular expressions on every edit, scroll, or watcher event.
- Made daily file merges write through a temporary file and rename so a crash mid-write cannot truncate a day of stored activity.
- Kept failed writes in a retry buffer instead of dropping them when persistence errors occur.
- Stopped tracking timers and event handling from starting when the local storage folder fails to initialize.
- Opened the dashboard immediately and loaded records over messages instead of embedding the full history in the page HTML.
- Inserted live records into sorted position instead of re-sorting the whole dataset on every commit.
- Reduced status bar updates to skip redraws when nothing changed and slow down while idle, paused, or disabled.
- Capped queued created-file metric captures during bursts such as git checkouts and build outputs.
- Tightened the dashboard content security policy to nonce-only scripts.
- Escaped spreadsheet formula characters at the start of CSV export cells.
- Aligned the extension bundle target and Node type definitions with the Node 18 runtime used by VS Code 1.85.

## 0.9.0

- Merged contiguous activity records sharing the same context into a single stored entry instead of opening a new one on every flush interval.
- Counted edited lines again after a short cooldown so sustained editing of the same lines keeps contributing to lines coded.
- Stopped crediting watcher-detected file creations (git checkouts, build outputs) as written content; only user-initiated creations count their lines and characters.
- Always excluded build artifact folders such as out-test, .vscode-test, coverage, and __pycache__ from tracking.
- Optimized dashboard rendering with cached record date keys, single-pass day grouping, debounced search, and tooltips that rebuild only when the hovered point changes.
- Split files created into its own dashboard stat line and added hover tooltips for truncated project and branch cells.
- Made chart axis labels, the goal ring, and all dashboard timers follow the selected clock, units, or decimal time format consistently.
- Persisted new activity within a few seconds of starting so fresh entries appear on the dashboard almost immediately.

## 0.8.0

- Added daily unique lines-coded and lines-read tracking, inserted-character totals, and created-file counts without storing source text.
- Added Lines coded and Lines read dashboard properties plus a dedicated line-activity donut chart.
- Included the new metrics in JSONL summaries and CSV exports, with proportional preservation across midnight splits.
- Made Git repository access persistent after built-in Git activation and enabled branch tracking by default for new records.
- Reworked project-ranking rows to wrap their progress track beneath the label and duration on narrow dashboard windows.

## 0.7.1

- Rebuilt project and language filter options whenever live records are appended or replaced.
- Cleared stale project and language names immediately after Reset Statistics replaces the dashboard dataset.

## 0.7.0

- Changed the Marketplace publisher identity to Fujimori and added a one-time copy migration for activity data stored under the legacy publisher ID.
- Made project tracking prefer Git repository names, then workspace names, with stable anonymous IDs only as a fallback.
- Changed new installs to store local repository/workspace names in full by default while retaining hashed and disabled privacy modes.
- Replaced Simple Icons with locally bundled, MIT-licensed Devicon SVGs and expanded programming-language coverage.
- Added Decimal view with adaptive seconds, minutes, hours, and days values rounded to at most two decimal places.

## 0.6.4

- Made Breakdown donut-chart duration labels scale against each ring's available inner width.
- Kept short duration labels at full size while safely fitting longer clock and unit formats inside the chart.

## 0.6.3

- Replaced the generic activity-list retention label with the exact selected date range and midnight boundary times.
- Kept the activity range label synchronized with calendar presets, custom ranges, and filter resets.

## 0.6.2

- Made chart and Momentum map tooltips dynamically flip to the left near the right edge so the hovered data point remains visible.
- Positioned tooltips using their rendered dimensions and target bounds for safer behavior across dashboard sizes.

## 0.6.1

- Removed seconds from the At a glance Daily Goal detail while preserving precision elsewhere.
- Reworked the Momentum map into wider fluid blocks with compact, consistent spacing.

## 0.6.0

- Fixed responsive canvas sizing so ring and donut charts remain circular in narrow editor layouts.
- Added a fourth At a glance ring for reading share.
- Expanded the Momentum map across its card and added detailed hover information.
- Kept the three Insights profile cards in one row at compact widths.
- Added properly cased language names and bundled Simple Icons brand marks without changing legend dots.
- Added compact toolbar actions for filter reset, backup, local-data access, and confirmed statistics reset.
- Updated author metadata to Fujimori and the display name to Celestia Time (feat. Celestiarinabelle).

## 0.5.0

- Added visibility-aware live dashboard snapshots and incremental committed-record updates.
- Suspended live traffic while hidden and added a one-time resync when returning to the dashboard.
- Throttled chart redraws to keep live analytics lightweight.
- Added visible data points for single-day daily charts that connect as more days accumulate.
- Fixed the calendar chevron open/closed animation.
- Hid status-bar seconds after one hour while keeping full dashboard precision.
- Added an Insights page with streaks, best-day and consistency metrics, a twelve-week momentum map, and focus profiles.

## 0.4.0

- Replaced every remaining native dashboard select with a consistent custom popover.
- Added activity multi-select filtering with colored dots, selection totals, record counts, and checkmarks.
- Added language-aware icons to the language filter.
- Enriched daily and hourly chart tooltips with icons and contextual metrics.
- Added large contextual icons to Analytics and Breakdown card headings.
- Fixed tiny donut slices incorrectly painting over the language chart.

## 0.3.0

- Changed the single status timer to open the dashboard and simplified its hover details.
- Added responsive activity cards for compact dashboard windows.
- Added 20-item activity pagination with first, previous, numbered, next, and last controls.
- Moved search into the page navigation and added a live clock/unit view selector.
- Added hover details to daily and hourly charts plus weekday/weekend rhythm filtering.
- Expanded Analytics with goal, coding-share, and top-project rings.
- Updated project rankings to use numbered positions.

## 0.2.0

- Replaced improvised dashboard glyphs with bundled Lucide icons.
- Added locally bundled Sora typography with no network dependency.
- Consolidated the status bar into one pulse-style timer.
- Added Activity, Analytics, and Breakdown dashboard views.
- Added daily trend, hourly distribution, goal, language, activity, and project charts.

## 0.1.0

- Added event-driven focused activity tracking.
- Added idle, blur, pause, and resume session handling.
- Added privacy-aware workspace, file, language, and optional Git metadata.
- Added local daily JSONL storage with retention cleanup.
- Added live status bar, seven-day dashboard, JSON/CSV export, and data deletion.
- Added interactive date ranges, search, filters, colored activity states, and Vercel-inspired activity rows.
- Added clock and explicit-unit duration formats.
- Added detailed status-bar goal progress.
