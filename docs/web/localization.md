# Localization

stepSOLAR supports four interface languages. All UI strings are loaded dynamically from the firmware translation table via `/api/i18n`.

## Supported Languages

| Code | Language | Flag |
|------|----------|------|
| `en` | English | 🇬🇧 |
| `sk` | Slovenčina | 🇸🇰 |
| `pl` | Polski | 🇵🇱 |
| `cs` | Čeština | 🇨🇿 |

## Switching Language

1. Use the language dropdown in the **top bar** (EN / SK / PL / CS)
2. The page re-fetches translations and updates all labels instantly
3. The selection is saved to EEPROM and restored on next boot
4. All open browser tabs sync to the same language via WebSocket

## Translation Architecture

Translations live in `include/i18n.h` as a static table:

```cpp
static const I18nEntry I18N_TABLE[] = {
  // key          EN              SK              PL              CS
  {"lbl_power",  "Instant Power","Okamžitý výkon","Moc chwilowa","Okamžitý výkon"},
  // ...
};
```

The `GET /api/i18n?lang=sk` endpoint serializes the selected column as a flat JSON object:

```json
{
  "lbl_power": "Okamžitý výkon",
  "lbl_voltage": "Napätie FV",
  "nav_monitor": "Monitor",
  ...
}
```

The browser stores this in the `T` object and looks up strings with `t("key")`.

## Adding a New Language

1. Add a new column to `I18N_TABLE` in `i18n.h`
2. Update the `Language` enum in `config.h`
3. Add the option to the `tr()` function switch
4. Add the `<option>` to the language selector in `index.html`
5. Re-flash firmware and web UI

## HTML Integration

All translatable elements use the `data-i18n` attribute:

```html
<div class="clabel" data-i18n="lbl_power">Instant Power</div>
```

On language load, `applyTranslations()` updates all elements:

```js
document.querySelectorAll('[data-i18n]').forEach(el => {
    el.textContent = T[el.getAttribute('data-i18n')] || el.textContent;
});
```
