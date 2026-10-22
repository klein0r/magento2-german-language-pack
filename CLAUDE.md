# CLAUDE.md

German language pack (`de_DE`) for Magento Open Source 2.4. Composer package `mkleine/magento2-german-language-pack`, Magento language package `mkleine_de_de`. Synced with Magento 2.4.9.

The repository contains no code, only data. Almost every task concerns `de_DE.csv`.

## Files

- `de_DE.csv`: all translations
- `language.xml`, `registration.php`, `composer.json`: package metadata, rarely change
- `LICENSE.txt`: OSL-3.0. Required because the package builds on the [splendidinternet package](https://github.com/splendidinternet/Magento2_German_LocalePack_de_DE). Do not remove the license or the attribution notice in the README.

## Scope: what belongs in `de_DE.csv`

Only phrases that Magento Open Source actually uses in the current version, including the bundled modules (`Magento_*`, `PayPal_*`, `lib/web`, themes `Magento/luma` and `Magento/blank`).

Do not include:

- Texts that exist only in Adobe Commerce (Gift Card, Reward Points, RMA, Gift Registry, Staging, etc.)
- Third-party extensions (Dotdigital, Amazon Pay, Klarna, Vertex, Yotpo, Amasty, FireGento, etc.)
- Entries whose translation is identical to the source (`P24,P24`, `SMTP,SMTP`). They have no effect, because Magento shows the source text anyway when there is no entry.
- Texts for which a core module ships its own `i18n/de_DE.csv`, e.g. the Ratepay legal texts in `paypal/module-braintree-core`. The language pack is loaded after the module translations and would overwrite the official wording.
- Junk from `i18n:collect-phrases`: code fragments (`'Error: ' .`, `"…"' . . '"`), test strings (`Error 1`, `Test message`, `Entity 1`), internal keys (`valid_label`, `modal_title_text`, `mediaUrlNotAvailable`) and plain numbers
- Duplicate keys. Each English text appears exactly once.

## CSV format

```
"English source","Deutsche Übersetzung",module,Magento_Catalog
```

- When loading a language pack, Magento only evaluates the first two columns (`Dictionary::readPackCsv`). Columns 3 and 4 are for orientation only and do not restrict anything to a module. Rows without a module have only two columns.
- Always write the file with PHP's `fputcsv($fh, $row, ',', '"', '\\')` and read it with `fgetcsv(…, ',', '"', '\\')`. This is the same escaping that Magento's `File\Csv` uses. Quotes are doubled (`""`). A backslash before a quote (`..\"`) is preserved.
- Sort order: alphabetical by English text, case-insensitive (`strcasecmp`), ties broken with `strcmp`.
- Never restructure the CSV by hand with `sed` or regex. Always parse and rewrite it. Afterwards verify that the row count equals the key count (see "Verification").

## Rules for the English key (column 1)

- **Never change the English source text, not even when it contains errors** (typos, grammar mistakes, double spaces, odd punctuation, inconsistent capitalization). Magento looks up translations by exact string match. A "corrected" key no longer matches the string in the code, and the translation silently stops working. Fix the mistake in the German translation instead, not in the key.
- The key must match, character for character, the string Magento passes to `__()` or `$t()`. Otherwise the translation never applies.
- **Multi-line keys** from `system.xml`, UI components or PHP strings contain real line breaks and the indentation from the source file. Do not trim or normalize them. Magento's config converter reads `<comment>`/`<label>` untrimmed.
- Leading or trailing spaces (`"Edit "`) are also part of the key.

## Rules for the translation (column 2)

### Language and style

- Always address the user informally with "du", both in the storefront and in the admin. Never use the formal "Sie".
  - Write "du", "dein", "dir", "dich" in lowercase, except at the start of a sentence.
  - Imperatives in the singular "du" form, with the "-e" ending where common: „Gib … ein“, „Wähle …“, „Klicke …“, „Versuche es erneut“, „Wende dich an …“.
  - "Sie"/"sie" meaning "they" or "it" (e.g. „Sie ist entweder zu alt …“) is not a form of address and stays.
- Natural German rather than literal translation. Error messages as full sentences: „Das Produkt mit der SKU "%1" existiert nicht."
- `Please …` becomes „Bitte …", `Something went wrong` becomes „Ein Fehler ist aufgetreten" or „Beim … ist ein Fehler aufgetreten", not „Etwas ist schiefgelaufen".
- Numbers and units in German notation when in running text: `21,9 %`, `1.200 £`. CSS values and example values (`50px`, `0.0 bis 1.0` as input format) stay unchanged.
- Use straight quotes `"…"` as in the source, no typographic quotes.
- Merge multi-line translations into a single line, unless the line break is semantically required (e.g. plain-text emails). All affected texts are rendered as HTML, where line breaks and indentation count as a single space anyway.
- No trailing line breaks or spaces if the key has none.

### Keep unchanged

- **Placeholders**: `%1`, `%2`, `%name`, `%store_name`, `%value%`, `%s`, `{{var …}}`, `{{trans …}}`. The order may change, the count may not.
- **HTML**: tags, attributes and URLs (`<a href="%1">`, `<strong class="colorRed">`, `<br />`). Only translate the visible text.
- **Technical identifiers**: field, parameter and class names (`customer_address_id`, `region_id`, `resetPasswordToken`, `GetAttributeValueInterface`, `\Braintree\Transaction`), constants and codes (`COUNTRY_ERROR`, `METHOD_UNAVAILABLE`), PHP functions (`exif_read_data()`), file names and paths (`query.xml`, `/media/sitemap/`), CLI commands, GraphQL types and queries (`Query.customerCart`, `CategoryFilterInput`).
- **Brand and product names**, never translate or Germanize:
  - Payments: PayPal, PayPal Credit, Braintree, Venmo, Apple Pay, Google Pay, Fastlane, Ratepay, Klarna, ACH, SEPA, iDEAL, Bancontact, BLIK, eps, MyBank, Przelewy24/P24, Visa, Mastercard, American Express, Discover
  - Shipping: DHL, FedEx, UPS, USPS and their service names (`Ground Advantage™`, `Priority Express`, `International Priority Express`)
  - Adobe/Magento: Magento, Adobe Commerce, Adobe IMS, Adobe ID, Adobe Stock, Experience League, Page Builder, Payment Services, Commerce Services Connector, Magento Open Source
  - Other services: Google, Google Maps, Google Analytics, reCAPTCHA, New Relic, NerdGraph, OpenSearch, Elasticsearch, AWS S3, Vimeo, YouTube, Duo Security, Swagger UI
- **Established technical terms and abbreviations**: SKU, URL, API, REST, GraphQL, CSV, JSON, XML, HTML, CSS, JavaScript, CAPTCHA, 2FA, U2F, WebAuthn, OTP, JWT, JWS, JWE, OAuth, CSP, SSL, TLS, SMTP, CVV, 3D Secure, IPN, CDN, Token, Cache, Cookie, Widget, Theme, Template (in the technical sense), Indexer, Cronjob, Webhook, Vault, Consumer, Session, Store View

### Glossary (preferred terms)

The existing entries are not consistent everywhere. New translations use these terms. Only align existing entries when explicitly requested.

| English | German |
|---|---|
| Shopping Cart / Cart | Warenkorb |
| Checkout (as button/page) | Kasse („Zur Kasse gehen") |
| Checkout (as process in running text) | Checkout |
| Wish List | Wunschzettel |
| Order | Bestellung |
| Reorder | Nachbestellen |
| Invoice | Rechnung |
| Credit Memo | Gutschrift |
| Shipment | Lieferung |
| Quote | Angebot |
| Billing Address | Rechnungsadresse |
| Shipping Address | Lieferadresse |
| Payment Method | Zahlungsart |
| Shipping Method | Versandart |
| Coupon Code | Gutscheincode |
| Cart Price Rule | Warenkorb-Preisregel |
| Catalog Price Rule | Katalogpreisregel |
| Customer Group | Kundengruppe |
| Attribute Set | Attributset |
| Storefront | Frontend |
| Admin / Admin Panel | Admin-Bereich / Admin-Panel |
| Store (as a shop) | Shop |
| Store (as scope, store ID) | Store |
| Website | Website |
| Grid | Grid (admin tables), Raster (Page Builder columns) |
| Source / Stock (MSI) | Quelle / Lager |
| Qty | Menge (in forms), Anzahl (in lists) |
| Email | E-Mail |
| Sign In / Sign Out | Anmelden / Abmelden |
| Review / Rating | Bewertung |
| Media Gallery | Mediengalerie |
| Swatch | Farbfeld |
| Template (content template, e.g. Page Builder) | Vorlage |
| Two-Factor Authentication | Zwei-Faktor-Authentifizierung |
| Login as Customer | Als Kunde anmelden |
| In-Store Pickup | Abholung im Geschäft |
| Pickup Location | Abholort |
| Pay Later | Später bezahlen |

## Phrases that stay untranslated

A core phrase may stay without an entry only for one of these reasons. Every untranslated phrase must be assigned to one of them individually.

1. **Brand, product or service name**: see the brand list under "Keep unchanged", including carrier rates and packaging names (`UPS Next Day Air`, `Priority Mail Flat Rate Envelope`) and payment products (`Payflow Pro`, `Express Checkout`).
2. **Established technical term or abbreviation**: see the list under "Keep unchanged", plus units (`cm`, `kg`, `px`) and formats (`CSV`, `Excel XML`).
3. **Identical German word**: the English word is also the correct German term in that UI context (`Status`, `Name`, `Newsletter`, `Export`, `Filter`, `Position`, `Layout`, `Online`). It must be the term a German UI would actually use. If a native German term is common, translate (`Home` → „Startseite", `Login` → „Anmelden", `Download` → „Herunterladen").
4. **Junk**: code fragments, test strings, internal keys and plain numbers, as listed under "Do not include".
5. **Own module translation**: a core module ships its own `i18n/de_DE.csv` for the phrase.

These are **not** valid reasons:

- The phrase was identical in a previous version of this pack or in another language pack.
- The key is multi-line, indented, long, or contains HTML.
- The text is only visible in the admin, or only in error messages.
- The key looks odd but is not one of the junk categories.

When a phrase is not clearly covered by reasons 1 to 5, translate it or ask.

## New Magento version: procedure

1. In a Magento installation of the new version, collect the phrases:
   `php -d memory_limit=-1 bin/magento i18n:collect-phrases -m -o phrases.csv`
2. Only consider core contexts: `module` with `Magento_*` or `PayPal_*`, `lib`, and `theme` with `Magento/*`. Filter out custom modules and third-party extensions.
3. Determine missing keys, i.e. phrases without an entry in `de_DE.csv`. Because identical entries are not kept in the file, this list also contains every phrase that was reviewed earlier and deliberately left in English. Review each phrase individually against "Phrases that stay untranslated"; never dismiss them as a group.
4. Translate according to the rules above. Take the keys exactly as collected (see "Rules for the English key").
5. Look for obsolete entries: keys that are confirmed neither by `collect-phrases`, nor by an `i18n/en_US.csv` under `vendor/magento`, `vendor/paypal` or `lib`, nor by a literal occurrence (`'…'`, `"…"`, `>…<`) in the core code. Remove unconfirmed texts.
6. Rewrite the file (sorted, deduplicated) and verify it.
7. Update `README.md` if the supported version changes.

`collect-phrases` does not find everything. Texts from data patches (e.g. attribute labels) or from `en_US.csv` are still translated at runtime. So always check all three sources from step 5 before treating an entry as obsolete.

## Verification

After every change to `de_DE.csv`:

- Read with `fgetcsv` (escape `\\`): row count equals key count, every row has 2 or 4 columns.
- Placeholders and HTML tags are identical in key and translation.
- No translation is identical to its key.
- No translation contains line breaks if the key contains none.
- No translation uses the formal address (`Sie`, `Ihr…`, `Ihnen` as a form of address).
- No existing English key was changed unintentionally (compare the set of keys before and after).
- Every core phrase without an entry is assigned to one of the reasons under "Phrases that stay untranslated". Report the count per reason, and list every untranslated phrase that is visible in the storefront explicitly.
- For multi-line or unusual keys: check against the runtime string, e.g. via `Magento\Config\Model\Config\Structure\Data` for `system.xml` texts.

## Working style

- The diff should contain only the intended entries. No mass reformatting and no silent glossary alignments.
- Helper scripts for comparison and verification do not belong in the repository unless explicitly requested.
- Flag translations with legal content (payment terms, privacy, right of withdrawal, financing information) for human review instead of silently adopting them.
