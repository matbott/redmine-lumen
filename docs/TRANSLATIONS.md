# Translations

Lumen translates the UI strings it adds to Redmine. Redmine itself keeps using its own
Rails locale files.

## How it works

The dictionary lives at the top of `javascripts/theme.js`:

```js
var RM_I18N = (function () {
  var en = { /* English */ };
  var es = { /* Spanish */ };
  var tables = { en: en, es: es };
  // ...
})();
```

- The language comes from `<html lang="es-PA">`, `<html lang="en">`, and so on.
- `es-PA` falls back to the `es` table.
- A language without a table falls back to English.
- A missing key falls back to English, then to the key name.
- `RM_I18N.locale` is the full locale and is used for dates and numbers.

## Using a string

```js
RM_I18N.t('lateTitle', { n: 12 })   // "Overdue (12)" / "Vencidas (12)"
```

Placeholders use braces: `{n}`, `{days}`, `{name}`, `{query}`, `{version}`. Never translate
the placeholder names themselves.

## Adding a language

1. Copy the English table in `javascripts/theme.js`.
2. Translate only the **values**, keeping every key and placeholder unchanged.
3. Add the language to the `tables` object:

   ```js
   var tables = { en: en, es: es, pt: pt };
   ```

4. Check the page with `<html lang="pt-BR">` in Redmine.
5. Add the language to the README and `CHANGELOG.md`.

Partial translations are fine: untranslated keys fall back to English. Please avoid
leaving a key with an empty value, because that hides the fallback.

## Conventions

- Use the same tone as Redmine's own translation for the language.
- Keep labels short; they are used in KPI cards, chips and table headers.
- Do not translate the internal keys (`lateTitle`, `myWeek`, …).
- Keep `{n}`, `{days}`, `{name}`, `{query}` and other placeholders exactly as they are.
- Dates and decimals use `RM_I18N.locale`, not a hard-coded locale.
- Punctuation and capitalisation should follow the target language.

## Current keys

The English and Spanish tables contain the same keys and cover:

- Dark/light mode and colour-scheme picker.
- Sidebar position and width.
- Collapsible issue sections.
- Project overview: KPIs, attention lists, donut chart, chips, Customise panel.
- Issue-list cards and due-date labels.
- My page KPIs, including logged hours this week.
- Theme version label.

The general Home dashboard introduced in `1.3.0` has its own English/Spanish dictionary
inside the home block, following the same fallback rules.

## Testing

At minimum:

1. `node --check javascripts/theme.js`
2. Load the theme with `lang="en"` and `lang="es"` (or your language).
3. Check the Home dashboard, the project overview, an issue list and **My page**.
4. Switch light/dark mode and confirm the toast uses the translated labels.
5. Confirm dates and decimals use the expected locale.
