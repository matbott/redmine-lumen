# Changelog

All notable changes to Lumen are documented here. The format follows
[Keep a Changelog](https://keepachangelog.com/en/1.1.0/) and the project uses
[Semantic Versioning](https://semver.org/).

## [1.8.2] — 2026-11-24

### Fixed

- RedmineUP select2 (the tags / multi-select widget in `redmineup.css`) no longer paints a
  white box that ignores the theme. The plugin ships the stock select2 default theme with
  hardcoded light colours — `background-color: white`, `border: 1px solid #ccc`, chips
  `#efefef`, focus `#5ad`, placeholder and arrow `#999` / `#888` — which looked out of place
  in light mode and was unreadable in dark mode, where the control stayed white while its
  text turned near-white. Every one of those values now follows the theme tokens, so a
  select2 field is indistinguishable from a native `<select>`: surface, border, radius and
  focus ring match `input` / `select` in `application.css`, selected chips take an
  accent tint, and the dropdown, its search field and the highlighted option use
  `--rm-surface` / `--rm-accent` / `--rm-on-accent`.
  Measured in dark mode: control `rgb(255,255,255)` → `rgb(30,44,58)`, border `#ccc` →
  `#3A5068`, radius `3px` → `8px`, chip `#efefef` → accent tint; dropdown `#1E2C3A` with
  the highlighted option on the accent. Contrast: 12.36:1 (text on surface), 9.39:1
  (on-accent on accent), 17.74:1 (light mode).

### Changed

- The theme version is now `1.8.2`.

## [1.8.1] — 2026-11-24

Bring Lumen level with `redmine-modern-theme-mf-v7` 1.8.1. Both themes now carry the same
capabilities and share a version number, so a fix validated on one ports cleanly to the other.

### Fixed

- No browser credential prompt on the project overview or My page. The REST API
  (`/issues.json`) does not authenticate by cookie: it answers `401` with
  `WWW-Authenticate` and the browser opens its login dialog, which no `fetch` can suppress.
  Every count now comes from the HTML listing with the session — `vzCount`/`vzTotal` read
  `.pagination .items` (falling back to counting rows and discarding `tr.nodata`), the
  attention panel builds its three lists the same way via `toItems()`, and MTTR reads
  `td.closed_on` / `td.created_on`. The theme makes no REST calls at all now.
- `queryUrl` serialised a range (`><`) as a single value — `2026-11-23,2026-11-30` — which
  Redmine rejects as an invalid date. It now emits two `v[]` entries. This also affected
  My page's "Due soon" link.
- No horizontal scroll on pages with a tab strip. `redmineup_cms.css` ships
  `#content .tabs ul { width: 10000px }` unconditionally, so `/settings`, `/issues` and
  `/time_entries` reported ~10 000px of scroll width inside `#content` and scrolled
  sideways. The theme's own `inline-size: auto` lost because the plugin sheet loads after
  it; the width is now pinned in `custom.css`. Measured on `/settings/edit`: tab strip
  10000px → 1129px, `#content` scrollWidth 10028px → 1185px, zero tabs cut, at 1440 / 1024
  / 780 / 600 / 420px. Do not "fix" this with `overflow-x: hidden` — that clips the last
  tabs with no way to reach them.
- RedmineUP Help & Support now styled on the real controller. The earlier
  `body.controller-help` selector never matched: the plugin renders `controller-questions`
  / `controller-questions_sections`. Section tiles, metric panels, ticket forms and journal
  bubbles follow the theme tokens in light and dark.
- Taskboard / board container background is scoped to the board pages
  (`:has(#rdb-taskboard)`, `:has(#rdb-board)`) instead of every page, so `#content` keeps
  its transparent background elsewhere and the scheme shows through in dark mode.
- Taskboard columns stack in a single column at ≤900px instead of overflowing the viewport.

### Added

- RedmineUP Vistazo hides message- and date-format custom fields in the issue details.
  Dormant on this instance (only `int_cf cf_3` exists) but correct as soon as such a field
  is defined. Core `start-date` / `due-date` attributes are untouched on purpose.
- Repeated world icon on Help & Support page fields is down to one glyph.

### Changed

- The theme version is now `1.8.1`, matching the MF v7 theme.

### Notes

- Redmine 7 renders settings on `controller-settings action-edit` at `/settings/edit`;
  `/admin/settings/configuration` does not exist. Selectors assuming `controller-admin`
  will silently do nothing.
- HTML counts cost more requests than the API did (six for the chips, three for the panel).
  If it ever drags, the proper fix is a Redmine plugin endpoint with `Issue.visible` and
  `GROUP BY tracker_id`, not going back to the REST API.

## [1.7.2] — 2026-11-20

### Fixed

- Taskboard / board now uses the theme surface and background tokens for the board and its
  table rows, covering the empty-board state instead of a plain white fallback.

### Changed

- The theme version is now `1.7.2`.

## [1.7.1] — 2026-11-17

### Fixed

- Removed the blank strip above RedmineUP CMS pages that embed an iframe (for example,
  Grafana dashboards). The CMS plugin adds a header-height top padding to `#content`; the
  theme now clears only that top offset while keeping the plugin's other spacing.

### Changed

- The theme version is now `1.7.1`.

## [1.7.0] — 2026-11-16

### Added

- Home **Open by type** is back, now as a **donut** of the **top 5 trackers by open count**
  and an **Others** slice for the remaining types.

### Changed

- The restored tracker donut loads a grouped issue list sequentially and scans at most
  **1,000 open issues**. With more open issues than that, it says it is based on the first
  1,000 and links to the full grouped list.
- The two Home panels share the row 50/50 again.

### Changed

- The theme version is now `1.7.0`.

## [1.5.0] — 2026-11-16

### Removed

- The Home **Open by type** panel, including its bars/donut selector, remaining-types
  footer, translations and dedicated styles. The experimental grouped issue-list request
  that loaded every page was removed after it caused excessive database load on a large
  instance.
- The unused Home helpers for tracker JSON and grouped-list parsing.

### Changed

- Home **Recent activity** now uses the full width below the KPI cards.
- The theme version is now `1.5.0`.

### Deferred

- An optional server-side stats plugin for permission-aware tracker counts across Home,
  project overview and issue lists is documented in `docs/DEVELOPMENT.md`. It is a larger
  change and is not part of this release.

## [1.4.0] — 2026-11-16

### Added

- Home **Open by type** now shows the **top 10 trackers by open count**, sorted from most
  to least, with a footer for the remaining types and their open issues. Data comes from
  one grouped issue list (all pages) instead of a per-tracker count.
- **Bars / Donut** selector for that chart. The donut shows the top 8 plus **Others** and
  reuses the project overview palette; the chosen view is remembered per browser.

### Changed

- The two Home panels, **Open by type** and **Recent activity**, now share the row 50/50.
- The theme version is now `1.4.0`.

## [1.3.0] — 2026-11-14

### Added

- General **Home dashboard** — a permission-aware overview above the welcome text with
  users (admin only), visible projects and issues, new/closed/updated in the last 7 days,
  logged hours, average closing time, open issues by tracker and recent activity.
- **Hours this week** on the My page KPI strip — logged hours for the Monday–Sunday week,
  read from the time-entry CSV with the current session. The card links to the filtered
  **time entries** page for the logged-in user and that week.

### Changed

- The theme version is now `1.3.0`.

## [1.2.5] — 2026-11-13

### Fixed

- Saved custom queries in the issues sidebar: the selected query and its **Clear (×)** link
  now stay on one row, with the × at the end, instead of pushing the clear link below the
  query and moving the following sidebar links down. The clear icon now uses the theme's
  muted text colour by default and the accent colour on hover/focus.

## [1.2.4] — 2026-11-13

First public Lumen release. This packages the 1.2.4 codebase, based on
[`acosonic/redmine-modern-theme`](https://github.com/acosonic/redmine-modern-theme)
(`redmine-7`, MIT).

### Added

- English and Spanish translations for the theme's own UI strings, selected from
  Redmine's `<html lang>` with an English fallback.
- Project overview hero with KPI cards, attention lists, donut chart and operational
  chips.
- Per-project **Customise** panel for indicators, attention lists, due-date colours and
  the stalled threshold.
- Issue-list KPI cards with open issues by tracker.
- “My overview” KPI strip on My page.
- Due-date colours and a true-overdue row marker in project issue lists.
- Initials avatars in the **Assigned to** and **Author** columns, using Redmine's avatar
  palette.
- Sidebar width setting on My account (180–360 px, per browser).
- Theme version displayed on My account.
- Lumen theme identity, documentation, license and attribution.
- Light and dark screenshot gallery for the project overview, issue list, My page and
  Administration, captured from the Lumen Demo project.
- Documented, tested plugin compatibility for RedmineUP Agile and Pins, Redmineflux
  Taskboard, Redmine Task Weight, RedmineUP Checklists, Testcase/Vault and View Customize.

### Fixed

- Dark-mode calendar cells, including the RedmineUP plugin overrides for weekends,
  week-number column and “today”.
- Centered toast notifications when switching light/dark mode.
- Header search width and the duplicated magnifier icon in autocomplete fields.
- RedmineUP plugin styles that shipped with light-only colours.

### Upstream

- Base: `acosonic/redmine-modern-theme`, branch `redmine-7`, commit `2685821`.
- Original author: Aleksandar Pavic.
