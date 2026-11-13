# Contributing to Lumen

Thanks for helping improve Lumen. Issues and pull requests are welcome.

## Before you start

- Lumen targets **Redmine 7.0+**. Changes that need a newer Redmine should say so.
- The theme is CSS and JavaScript only. Please do not add backend code, gems or plugins.
- Keep upstream code easy to rebase. Put fork-specific styles in
  `stylesheets/custom.css` and mark new script blocks clearly in
  `javascripts/theme.js`.

## Reporting a bug

Include:

- Redmine version.
- Browser and version.
- Theme version (shown on **My account**).
- Which mode and colour scheme you were using.
- Steps to reproduce, expected result and what happened.
- A screenshot if the issue is visual.
- Any installed plugins that affect the page.

## Pull requests

1. Fork the repository and create a branch from `main`.
2. Keep the change focused. Do not reformat unrelated files.
3. Run the syntax check:

   ```bash
   node --check javascripts/theme.js
   ```

4. If you touch translations, keep the English and Spanish tables in sync and add the
   new keys to both. See [`docs/TRANSLATIONS.md`](docs/TRANSLATIONS.md).
5. Test light mode, dark mode and at least two colour schemes.
6. Describe the change and the verification in the pull request.

## Translations

The fastest way to contribute is a new language table in `RM_I18N`
(`javascripts/theme.js`). Copy the English table, translate the values, keep the keys and
placeholders unchanged. Add the language to the `tables` object and mention it in the
pull request.

If you prefer, open an issue with the translated strings and we can wire them up.

## Upstream

Lumen is based on
[`acosonic/redmine-modern-theme`](https://github.com/acosonic/redmine-modern-theme)
(`redmine-7`). When syncing upstream changes, preserve the original copyright and
attribution, and keep Lumen's local blocks clearly separated.

## Code style

- Plain ES5-compatible JavaScript, matching the surrounding code.
- Use the `--rm-*` design tokens. Avoid hard-coded colours.
- Use logical CSS properties (`margin-inline`, `padding-block`, …).
- `!important` only to override plugin CSS or inline styles; explain why in a comment.
- Prefer accessible markup: labels, `aria-*`, keyboard support and visible focus.

## License

By contributing, you agree that your contribution is licensed under the MIT License in
[`LICENSE`](LICENSE).
