# Changelog

All notable changes to Lumen are documented here. The format follows
[Keep a Changelog](https://keepachangelog.com/en/1.1.0/) and the project uses
[Semantic Versioning](https://semver.org/).

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

### Fixed

- Dark-mode calendar cells, including the RedmineUP plugin overrides for weekends,
  week-number column and “today”.
- Centered toast notifications when switching light/dark mode.
- Header search width and the duplicated magnifier icon in autocomplete fields.
- RedmineUP plugin styles that shipped with light-only colours.

### Upstream

- Base: `acosonic/redmine-modern-theme`, branch `redmine-7`, commit `2685821`.
- Original author: Aleksandar Pavic.
