# Zdroje pro přednášku na WP pivu o AI toolingu, 16. října 2025

```
 ▐▛███▜▌   Claude Code v2.0.15
▝▜█████▛▘  Sonnet 4.5 · Claude API
  ▘▘ ▝▝    /Andelsky-pivovar/WP-pivo/16-rijen-2025
```


Daniel Mejta, https://wpify.cz
Karolína Vyskočilová, https://kybernaut.cz

## Návody, tipy a triky

### Screenshots velkých stránek
Jak efektivně screenshotovat dlouhé stránky, aby nedošlo k zamrznutí Claude. Strategie zahrnuje kontrolu výšky stránky a podle toho buď full-page screenshot nebo targetovaný viewport screenshot.

Viz [how-to/Screenshots-large-images.md](how-to/Screenshots-large-images.md)

### WordPress a WooCommerce MCP server
Jak nastavit MCP server pro práci s WordPress a WooCommerce API, včetně konfigurace pro localhost a JWT autentizaci.

Viz [how-to/WordPres-mcp.md](how-to/WordPres-mcp.md)

### Claude Code Hooks
Jak nastavit notifikace při dokončení úkolu nebo při potřebě inputu pomocí hooks v `settings.json`. Příklad používá `terminal-notifier` pro macOS notifikace.

Viz [how-to/Hooks.md](how-to/Hooks.md)

## Vlastní slash commandy

### /create-command

Umožňuje inteligentně vytvářet nové custom slash commandy pro Claude Code. Command analyzuje vaše požadavky, ověří si aktuální dokumentaci a vygeneruje dobře strukturovaný command soubor s validací, příklady použití a best practices.

**Použití:**
```
/create-command [cesta/nazev-prikazu] [popis funkcionality]
```

Command najdete v [.claude/commands/create-command.md](.claude/commands/create-command.md)

### /commit

Analyzuje všechny necommitované změny v repository a vytvoří komplexní, dobře strukturovanou commit zprávu, která přesně popisuje změny. Automaticky staguje a commitne všechny upravené soubory.

**Použití:**
```
/commit
/commit [typ-commitu]  # např. feature, fix, refactor
```

Command najdete v [.claude/commands/commit.md](.claude/commands/commit.md)

### /tag

Vytvoří git tag s automatickým nebo manuálním semantic version bumpem. Analyzuje commit zprávy a změny souborů pro inteligentní určení vhodného incrementu verze (patch, minor, major).

**Použití:**
```
/tag                  # auto-detekce version bumpu
/tag patch|minor|major  # explicitní specifikace
```

Command najdete v [.claude/commands/tag.md](.claude/commands/tag.md)

### /changelog

Inteligentně analyzuje necommitované změny a git historii od posledního tagu, kategorizuje je podle Keep a Changelog formátu a aktualizuje sekci `[Unreleased]` ve vašem changelog souboru.

**Použití:**
```
/changelog
```

Command najdete v [.claude/commands/changelog.md](.claude/commands/changelog.md)
