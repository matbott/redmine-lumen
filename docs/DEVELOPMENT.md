# Development notes

Internal notes for maintaining Lumen without rebuilding context from scratch.

## Identity

- Theme id / folder: `redmine-lumen`
- Display name: `Lumen`
- Current version: `1.2.4`
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

- `custom.css` — customisations, plugin fixes, calendar dark mode, toast position.
- `theme.js` — `RM_THEME`, `RM_I18N`, upstream code and local blocks for:
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

## My page

- Resolve the user id from the account flyout (`/users/<id>`), falling back to `me`.
- Filter URLs use `op[assigned_to_id]=` with `v[assigned_to_id][]=<id>`.
- API calls use `assigned_to_id=<id>`.

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
2. Project overview: KPIs, donut switch, attention lists, chips, Customise panel.
3. Issue list: cards, due colours, avatars, column alignment.
4. My page: six KPIs and links.
5. Calendar in dark mode.
6. Header search width and autocomplete magnifier.
7. Light/dark toggle: centered toast and correct scheme.
8. My account: schemes, sidebar position/width and version.

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
- Screenshots and a demo gallery.
- Optional Ctrl+K search, favourites and quick filters.
- Grafana iframe theme sync (outside the theme itself).
