# Changelog

All notable changes to Lumen are documented here. The format follows
[Keep a Changelog](https://keepachangelog.com/en/1.1.0/) and the project uses
[Semantic Versioning](https://semver.org/).

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
