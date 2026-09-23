# Design: [naam van het project]

Claude Code volgt dit bestand strikt bij alle UI-werk. Vul alleen in wat vaststaat en verwijder kopjes die niet van toepassing zijn.
Staat iets hier niet in? Kies dan de simpelste neutrale optie en gebruik bestaande componenten en variabelen uit het project.

## Algemene stijl

[2 tot 4 zinnen over de sfeer, bijvoorbeeld: sober, zakelijk, veel witruimte, weinig decoratie.]

## Kleuren

| Rol | Naam | Hex |
|---|---|---|
| Hoofdkleur | [naam] | #000000 |
| Accent | [naam] | #000000 |
| Achtergrond | [naam] | #FFFFFF |
| Tekst | [naam] | #111111 |
| Tekst gedempt | [naam] | #666666 |
| Rand | [naam] | #E5E5E5 |
| Succes | [naam] | #000000 |
| Fout | [naam] | #000000 |

- Gebruik kleuren via CSS-variabelen, nooit hardcoded hexcodes in componenten.
- Minimaal contrast: WCAG AA.

## Typografie

- Lettertype koppen: [naam, bijvoorbeeld Inter]
- Lettertype tekst: [naam, of hetzelfde als koppen]
- Fallback: [bijvoorbeeld system-ui, sans-serif]
- Groottes: [bijvoorbeeld h1 2rem, h2 1.5rem, h3 1.25rem, tekst 1rem]
- Regelhoogte: [bijvoorbeeld 1.5 voor lopende tekst]
- Gewichten: [bijvoorbeeld 400 voor tekst, 600 voor koppen]

## Spacing en layout

- Spacing-schaal: [bijvoorbeeld 4, 8, 16, 24, 32, 48 px]
- Maximale breedte content: [bijvoorbeeld 1200px]
- Grid: [bijvoorbeeld 12 kolommen, gutter 24px]
- Breakpoints: [bijvoorbeeld 640px, 768px, 1024px, 1280px]
- Mobile first: [ja of nee]

## Componenten

### Knoppen
- Primair: [kleur, hoekradius, padding, hover]
- Secundair: [...]
- Uitgeschakeld: [...]

### Formulieren
- Invoervelden: [rand, hoekradius, hoogte, focus-stijl]
- Labels: [boven het veld of ernaast]
- Foutmeldingen: [kleur, plek]

### Kaarten, tabellen en navigatie
- [Beschrijf alleen wat het project gebruikt]

## Afbeeldingen en iconen

- Iconenset: [bijvoorbeeld Heroicons, Lucide, of geen]
- Afbeeldingsstijl: [bijvoorbeeld foto's zonder filter, vaste verhouding 16:9]
- Logo: [pad naar bestand en gebruiksregels]

## Tone of voice

- Taal van de interface: [Nederlands of Engels]
- Aanspreekvorm: [je of u]
- Stijl: [bijvoorbeeld kort, direct en zonder marketingtaal]

## Vermijden

- Geen lange streepjes (em dash of en dash) in teksten.
- Geen emoji in de interface.
- Geen gradients, glow-effecten of zware schaduwen, tenzij hierboven anders staat.
- [Projectspecifieke dingen die je niet wilt zien]
