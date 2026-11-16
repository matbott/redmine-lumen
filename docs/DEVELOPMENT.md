# Development notes

Internal notes for maintaining Lumen without rebuilding context from scratch.

## Identity

- Theme id / folder: `redmine-lumen`
- Display name: `Lumen`
- Current version: `1.7.0`
- Upstream: [`acosonic/redmine-modern-theme`](https://github.com/acosonic/redmine-modern-theme),
  branch `redmine-7`, commit `2685821`, MIT.
- Original author: Aleksandar Pavic.
- Maintainer: Matias Fusaro (`@matbott`), with OpenCode as AI collaborator.

The id, display name and version live in `RM_THEME` at the top of
`javascripts/theme.js`. The version is shown at the end of `#rm-layout` on
**My account**.

## Layout

```
stylesheets/application.css   upstream entry point; only adds @import of custom.css
stylesheets/schemes.css       generated schemes
stylesheets/custom.css        all Lumen/plugin adjustments
javascripts/theme.js          upstream + Lumen blocks + RM_I18N + RM_THEME
src/                          local development helpers, not loaded by Redmine
docs/                         development and translation notes
```

Redmine loads `stylesheets/application.css` and `javascripts/theme.js` from the theme
folder. Keep those entry points working.

## Cascade and tokens

- Redmine 7 core CSS is imported into `@layer(core)`.
- Theme rules are unlayered and therefore win over core.
- Lumen uses `--rm-*` tokens so light mode, dark mode and every scheme follow.
- `!important` is reserved for plugin CSS and inline styles. Explain each occurrence.

## Local blocks

- `custom.css` — customisations, plugin fixes, calendar dark mode, toast position, Home.
- `theme.js` — `RM_THEME`, `RM_I18N`, upstream code and local blocks for:
  - Home dashboard (permission-aware general dashboard, HTML reads with the current
    session, top-5 open-by-tracker donut capped at 1,000 open issues, and recent activity);
  - project overview hero, KPIs, attention, donut and Customise panel;
  - issue-list cards and due-date colours;
  - My page KPI strip;
  - initials avatars;
  - sidebar width and version display.

When syncing upstream, keep the local blocks clearly delimited and preserve the original
copyright notice.

## Per-browser settings

All settings are client-side, in `localStorage`:

| Key | Scope |
| --- | --- |
| `rm-dark` | light/dark mode |
| `rm-scheme` | active built-in scheme |
| `rm-scheme-gogh` | active custom Gogh scheme |
| `rm-sidebar-side` | sidebar left/right |
| `rm-sidebar-width` | sidebar width |
| `rm-vz-stats:<project>` | project dashboard and due-colour toggles |
| `rm-open-<section>` | collapsible section state |

`rm-home-trackers-view` was used by the removed Home tracker chart. It is no longer read;
leaving it in a browser is harmless.

There is no backend configuration. To share project defaults, a custom field or plugin
would be needed.

## Project overview

- Only runs on `controller-projects action-show`.
- Settings default to on; the stalled threshold defaults to 7 days.
- Metrics come from the server-rendered issue report and `/issues.json`.
- Attention lists fetch up to four issues per section.
- The donut uses the tracker report and the assignee report.
- The red overdue marker uses `tr.vz-row-late`, not `behind-schedule` (that flag also
  covers issues not yet due, based on `done_ratio`).

## Issue lists

- Cards run only on `/projects/<id>/issues`.
- They are a fixed snapshot of open issues by tracker and do not follow active filters.
- Due-date colours require the due-date column to be visible.

## Deferred: optional stats plugin

The Home all-pages grouped-list loader was retired in `1.5.0`. The tracker donut restored
in `1.7.0` uses a bounded grouped-list scan: sequential pages, at most 1,000 open issues
scanned and a footer link to the full grouped list.

If tracker breakdowns are wanted again without the bounded client-side scan, use a small
Redmine plugin instead of reading issue-list HTML:

- One permission-aware endpoint, for example `GET /lumen_stats.json?scope=home` and
  `GET /lumen_stats.json?project_id=<identifier>`.
- Use `Issue.visible(User.current)` and check `view_issues` for project stats; never return
  counts from projects the user cannot see.
- Return aggregate totals and `GROUP BY tracker_id` counts. Add the 7-day and attention
  counts to the same response if desired.
- Cache the JSON with `Rails.cache` (about 5 minutes) keyed by user, scope, project and
  relevant parameters. The first request after expiry runs one aggregate query; later
  page loads are instant.
- Keep recent-activity and attention lists as limited `/issues.json` reads. The plugin is
  for counts, not for listing issue rows.
- The theme should treat the plugin as optional: if the endpoint is missing, hide the
  tracker breakdown and keep the rest of Home working.
- This is a larger change because it adds a backend component and must be tested against
  permissions and a 50k-issue instance. It is not implemented yet.

## My page

- Resolve the user id from the account flyout (`/users/<id>`), falling back to `me`.
- Filter URLs use `op[assigned_to_id]=` with `v[assigned_to_id][]=<id>`.
- API calls use `assigned_to_id=<id>`.
- The logged-hours card reads `/time_entries.csv?from=<monday>&to=<sunday>&user_id=<uid>&c[]=hours`
  with the current session, and sums the CSV. The week is Monday–Sunday of the current
  calendar week; the card links to `/time_entries?from=<monday>&to=<sunday>&user_id=<uid>`.
  Hours respect private projects through the session-authenticated request.

## Calendar

Core paints the grid with static open-colors. The RedmineUP plugin stylesheet loads after
the theme and repaints weekends, the week-number column and “today” with light colours.
The calendar rules in `custom.css` use `!important` for those plugin overrides.

## Toast

The upstream responsive rule for `#rm-toast-container` sets `left/right` but keeps
`transform: translateX(-50%)`, which pushes the toast off-screen. Lumen restores centering
with `html #rm-toast-container` (higher specificity, no `!important`) and caps the width to
`min(420px, calc(100vw - 24px))`.

## Translations

See [`TRANSLATIONS.md`](TRANSLATIONS.md). Keep the English and Spanish tables in sync.

## Testing

Minimum smoke tests:

```bash
node --check javascripts/theme.js
```

Manual checks:

1. Install the theme in Redmine 7 and select it.
2. Home dashboard: permission-aware cards, top-5 donut with Others, 50/50 panels, footer
   “view all”, capped grouped scan; admin, user and anonymous views.
3. Project overview: KPIs, donut switch, attention lists, chips, Customise panel.
4. Issue list: cards, due colours, avatars, column alignment.
5. My page: seven KPIs (including logged hours this week) and links.
6. Calendar in dark mode.
7. Header search width and autocomplete magnifier.
8. Light/dark toggle: centered toast and correct scheme.
9. My account: schemes, sidebar position/width and version.

Automated tests are not bundled. The prior development used `jsdom` fixtures for My page,
issue lists, due dates and avatars; those fixtures are local and not part of the theme.

## Deployment

```bash
docker exec -it <container> /bin/bash -c '
  cd /usr/src/redmine &&
  rm -rf public/assets/* &&
  RAILS_ENV=production bundle exec rake tmp:cache:clear &&
  RAILS_ENV=production bundle exec rake assets:precompile'
docker restart <container>
```

Then hard-refresh the browser (`Ctrl+F5`). If assets are served from a CDN, purge it too.

## Release checklist

1. Update `RM_THEME.version`.
2. Update `CHANGELOG.md`.
3. Run `node --check javascripts/theme.js`.
4. Commit, tag `vX.Y.Z`, push `main` and the tag.
5. Create a GitHub release with the changelog section.
6. Deploy and verify the version on **My account**.

## Backlog

- More languages (Portuguese, French, German) and a language contribution guide.
- Configurable stalled threshold on My page.
- Due-date colours on the global issue list.
- 60/90-day due bands.
- Project dashboard preferences shared through a custom field or plugin.
- Screenshots for mobile, Gantt, calendar and issue detail views.
- Optional Ctrl+K search, favourites and quick filters.
- Grafana iframe theme sync (outside the theme itself).
