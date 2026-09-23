# Project: [naam van het project]

Projectspecifieke regels voor Claude Code. Deze staan in `/docs/project.md`. Design staat apart in `/docs/design.md`.
Vul alleen in wat echt nodig is en houd het kort. Verwijder kopjes die niet van toepassing zijn.

## Omschrijving

[1 tot 3 zinnen: wat doet dit project en voor wie?]

## Stack en versies

- Taal en versie: [bijvoorbeeld PHP 8.3]
- Framework en versie: [bijvoorbeeld Laravel 11]
- Frontend: [bijvoorbeeld Twig, Tailwind, vanilla JS]
- Database: [bijvoorbeeld MySQL 8]
- Hosting: [bijvoorbeeld Plesk shared hosting]

## Dev-omgeving

- Starten: `[bijvoorbeeld docker compose up -d]`
- Stoppen: `[bijvoorbeeld docker compose down]`
- Commando's uitvoeren in de container: `[bijvoorbeeld docker compose exec app php artisan migrate]`
- Lokale URL: [bijvoorbeeld http://localhost:8080]
- Gebruik niet: [bijvoorbeeld php artisan serve, want dev draait via Docker]

## Mappenstructuur

- `[map]`: [waar staat wat, bijvoorbeeld app/Http/Controllers voor controllers]
- `[map]`: [...]
- Losse JS staat in: `[pad]`
- Losse CSS staat in: `[pad]`

## Conventies

- Naamgeving: [bijvoorbeeld camelCase voor variabelen, PascalCase voor klassen]
- Formatter en linter: [bijvoorbeeld Pint, ESLint, Prettier]
- Taal van teksten in de interface: [bijvoorbeeld Nederlands]

## Testen

- Testcommando: `[bijvoorbeeld docker compose exec app php artisan test]`
- Linter: `[commando]`
- Wat altijd getest moet worden: [bijvoorbeeld nieuwe endpoints, formulieren]

## Git

- Branchstrategie: [bijvoorbeeld issue branches als feature/401-login, of alleen main en dev]
- Commit format: [bijvoorbeeld feat(#401): added login, of feat: added login]
- Hoofdbranch: [main of dev]

## Afspraken en uitzonderingen

- [Projectspecifieke afspraak, bijvoorbeeld: alle formulieren gebruiken de bestaande FormRequest klassen]
- [Uitzondering op de algemene regels, bijvoorbeeld: in dit project mag jQuery, want het zit er al in]

## Niet aanraken

- [Bestanden of mappen die Claude niet mag wijzigen, bijvoorbeeld vendor/, legacy/ of gegenereerde bestanden]
