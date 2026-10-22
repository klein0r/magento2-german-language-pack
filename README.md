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

`de_DE.csv` enthält pro Zeile den englischen Originaltext und die deutsche Übersetzung. Die optionalen Spalten 3 und 4 (`module,Magento_X`) dienen nur der Orientierung, Magento wertet beim Laden eines Sprachpakets ausschließlich die ersten beiden Spalten aus.

Mehrzeilige Originaltexte (z. B. aus `system.xml` oder UI-Components) müssen inklusive Zeilenumbrüchen und Einrückung exakt so bleiben, wie Magento sie liest, sonst greift die Übersetzung nicht.

## Herkunft und Lizenz

Dieses Paket basiert auf dem [Magento 2 German LocalePack](https://github.com/splendidinternet/Magento2_German_LocalePack_de_DE) von Splendid Internet und dessen Mitwirkenden. Es wurde um veraltete Einträge bereinigt, dedupliziert und um fehlende Übersetzungen für Magento 2.4.9 ergänzt.

Lizenziert unter der Open Software License 3.0 (OSL-3.0), siehe [LICENSE.txt](LICENSE.txt).
