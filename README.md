# Magento 2 German Language Pack (de_DE)

Deutsches Sprachpaket für Magento Open Source 2.4 (abgeglichen mit Version 2.4.9).

Enthalten sind nur Übersetzungen für Texte, die Magento Open Source 2.4.9 inklusive der mitgelieferten Module (z. B. PayPal, Braintree, Payment Services, Adobe IMS/Stock) tatsächlich verwendet. Übersetzungen für Adobe Commerce oder Extensions von Drittanbietern sind nicht enthalten.

## Installation mit Composer

```bash
composer require mkleine/magento2-german-language-pack
php bin/magento setup:static-content:deploy -f de_DE
php bin/magento cache:clean
```

## Manuelle Installation

Alle Dateien nach `app/i18n/mkleine/de_de/` kopieren, danach:

```bash
php bin/magento setup:static-content:deploy -f de_DE
php bin/magento cache:clean
```

## Format

`de_DE.csv` enthält pro Zeile den englischen Originaltext und die deutsche Übersetzung. Beide stehen immer in doppelten Anführungszeichen:

```
"Sample","Muster",module,Magento_Downloadable
```

Die optionalen Spalten 3 und 4 (`module,Magento_X`) dienen nur der Orientierung, Magento wertet beim Laden eines Sprachpakets ausschließlich die ersten beiden Spalten aus. Nach jeder Änderung `php bin/check format` ausführen. Der Befehl sortiert die Datei und setzt die Anführungszeichen einheitlich.

Mehrzeilige Originaltexte (z. B. aus `system.xml` oder UI-Components) müssen inklusive Zeilenumbrüchen und Einrückung exakt so bleiben, wie Magento sie liest, sonst greift die Übersetzung nicht.

## Prüfen und pflegen

Das Repository enthält mit `bin/check` ein Prüf-Tool (PHP 8.1+, ohne Abhängigkeiten). Es wird nicht mit dem Composer-Paket ausgeliefert.

```bash
# de_DE.csv sortieren und einheitlich formatieren (Spalte 1 und 2 immer in Anführungszeichen)
php bin/check format

# Format und Inhalt von de_DE.csv prüfen (läuft auch als GitHub Action bei jedem Push)
php bin/check verify

# Core-Phrasen einer Magento-Installation finden, die im Paket fehlen
php bin/check missing /pfad/zu/magento

# Einträge finden, die Magento nicht mehr übersetzt
php bin/check obsolete /pfad/zu/magento
```

`missing` und `obsolete` rufen `bin/magento i18n:collect-phrases` selbst auf, das dauert etwa eine Minute. Mit `--phrases=DATEI` lässt sich eine bereits erzeugte Ausgabe wiederverwenden.

`verify` meldet Fehler und Warnungen und schlägt nur bei Fehlern fehl:
- **Fehler:** Spaltenzahl, doppelte Schlüssel, Sortierung, Format, Platzhalter, identische Einträge, Zeilenumbrüche, zusätzliche CSV-Dateien
- **Warnungen:** veränderte HTML-Tags, Sie-Form, veränderte Markennamen, Junk-Schlüssel und uneinheitlich übersetzte Schreibvarianten. Sie sind Hinweise zur Durchsicht.

## Welche Texte enthalten sind

Das Paket enthält nur Texte, die Magento tatsächlich übersetzt. Für jeden Eintrag muss mindestens eine dieser Quellen ihn belegen:

1. **`bin/magento i18n:collect-phrases -m`**: Das ist Magentos eigener Sammler. Er findet Texte in `__()`, `$t()`, `$.mage.__()` und in XML-Knoten mit `translate="…"`. Berücksichtigt werden nur Core-Kontexte (`Magento_*`, `PayPal_*`, `lib`, Themes `Magento/*`).
2. **`i18n/en_US.csv` der Core-Module** unter `vendor/magento`, `vendor/paypal` und `lib`.
3. **Ein wörtlicher Treffer im Core-Code, der zur Laufzeit übersetzt wird.** Diese Quelle ist nötig, weil `collect-phrases` nicht alles findet, z. B. Attribut-Labels aus Setup-Skripten oder die Labels von Produkttypen und E-Mail-Vorlagen.

### Wie die wörtliche Suche funktioniert

Gesucht wird nach dem englischen Text, eingeschlossen in `'…'`, `"…"` oder `>…<`. Durchsucht werden Dateien mit den Endungen `.php`, `.phtml`, `.js`, `.html`, `.xml` und `.graphqls` unter `vendor/magento`, `vendor/paypal` und `lib`. Ein Beispiel für einen einzelnen Text:

```bash
grep -rnF -e "'Tier Price'" -e '"Tier Price"' -e '>Tier Price<' \
  --include='*.php' --include='*.phtml' --include='*.js' --include='*.html' \
  --include='*.xml' --include='*.graphqls' \
  vendor/magento vendor/paypal lib | grep -v '/i18n/'
```

Die ersten Treffer dieses Befehls sind Spaltenkommentare aus `db_schema.xml`, die Magento nicht übersetzt. Der eigentliche Beleg ist `'label' => 'Tier Price'` in `module-catalog/Setup/CategorySetup.php`.

### Wie man falsche Treffer ausschließt

Ein wörtlicher Treffer allein reicht nicht. Bei der ersten Bereinigung waren rund zwei Drittel dieser Treffer Texte, die Magento nie übersetzt. Jede Fundstelle muss deshalb einzeln geprüft werden: Läuft der Text dort durch den Übersetzer?

Als Beleg zählen:

| Fundstelle | Beispiel |
|---|---|
| Attribut-Definitionen in `Setup/` und `Setup/Patch/Data/` (`'label'`, `'note'`, Gruppennamen) | `'label' => 'Tier Price'` |
| `label` in `product_types.xml`, `email_templates.xml`, `import.xml`, `export.xml` | `<type name="simple" label="Simple Product">` |
| `title` in `menu.xml` und `acl.xml`, `label` von Layout-Handles | `title="Braintree Settlement"` |
| Labels und Texte in UI-Components (`view/*/ui_component/*.xml`) | `<label>Search Engine Robots</label>` |
| `<label>` und `<comment>` in `etc/adminhtml/system.xml` | |
| Übersetzungsaufrufe in JS, HTML und PHP: `$t()`, `$.mage.__()`, `_t()`, `i18n:`, `$this->translate()` | `<!-- ko i18n: "Not available" -->` |

Nicht als Beleg zählen:

| Fundstelle | Beispiel |
|---|---|
| Spaltenkommentare in `db_schema.xml` | `comment="Store ID"` |
| Standardwerte in `config.xml` | `<name>Owner</name>` |
| Konstanten, `case`-Zweige, Array-Schlüssel und -Werte, die verglichen oder an eine API geschickt werden | `case 'Failed':`, `'class_name' => 'Taxable Goods'` |
| Texte, die nur als Wert in eine Meldung eingesetzt werden | `__('"%1" must be later …', 'Expiration date')` |
| Exceptions und Seitentitel ohne `__()` | `new \DomainException('…')` |
| Variablenbeschreibungen in E-Mail-Vorlagen | `"var order.increment_id":"Order Id"` |
| Docblocks, Code-Kommentare, GraphQL-`@doc` | `@doc(description: "Upsell Products")` |
| Teile eines längeren Textes | `'<span class="label">Order Date:</span> %1'` |
| Tests, Fixtures und Generatoren | `Test/`, `dev/tests/`, `Setup/Fixtures`, `FixtureGenerator` |
| Fremdbibliotheken mit eigener oder ohne Übersetzung | `lib/web/hugerte`, `lib/web/css/docs`, `*.min.js` |

Im Zweifel den umgebenden Code lesen und prüfen, ob der Text bei `__()`, `$t()` oder einem der oben genannten Mechanismen ankommt.

### Groß- und Kleinschreibung

Magento sucht Übersetzungen exakt, also auch nach Groß- und Kleinschreibung, Satzzeichen und Leerzeichen. `Learn More` und `Learn more` sind deshalb zwei verschiedene Einträge. Beide gehören ins Paket, wenn beide Schreibweisen im Code übersetzt werden, und sie sollten gleich übersetzt sein. Eine Variante, die keine der Quellen oben belegt, gehört nicht ins Paket.

## Herkunft und Lizenz

Dieses Paket basiert auf dem [Magento 2 German LocalePack](https://github.com/splendidinternet/Magento2_German_LocalePack_de_DE) von Splendid Internet und dessen Mitwirkenden. Es wurde um veraltete Einträge bereinigt, dedupliziert und um fehlende Übersetzungen für Magento 2.4.9 ergänzt.

Lizenziert unter der Open Software License 3.0 (OSL-3.0), siehe [LICENSE.txt](LICENSE.txt).
