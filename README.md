# claude-setup

Een template met vaste regels voor Claude Code, zodat hij zich in al mijn ICT-projecten hetzelfde gedraagt.

## Inhoud

```
claude-setup/
├── .claude/
│   └── CLAUDE.md
├── docs/
│   ├── project.md
│   └── design.md
└── README.md
```

| Bestand | Doel |
|---|---|
| `.claude/CLAUDE.md` | Algemene werkwijze: communicatie, scope, code, mappen, git, testen en veiligheid. Voor al mijn projecten gelijk. |
| `docs/project.md` | Projectspecifieke regels: stack, dev-commando's, mappenstructuur, conventies, git-afspraken en uitzonderingen. |
| `docs/design.md` | Design van het project: kleuren, typografie, spacing en componenten. |

## Gebruik

### Nieuw project starten

1. Klik op **Use this template** bovenaan de repo op GitHub en maak een nieuwe repo aan.
2. Vul `docs/project.md` in. Verwijder kopjes die niet van toepassing zijn.
3. Vul `docs/design.md` in, of verwijder het bestand. Ontbreekt het, dan houdt Claude Code zich aan de standaard "niet AI-generated" regels uit `CLAUDE.md`.
4. Verwijder alle `[placeholders]` die je niet invult. Lege placeholders kunnen Claude Code in de war brengen.

### Globaal instellen (eenmalig)

Wil je de regels in al je projecten, ook zonder template, kopieer dan `.claude/CLAUDE.md` naar je home-map:

- macOS en Linux: `~/.claude/CLAUDE.md`
- Windows: `C:\Users\<naam>\.claude\CLAUDE.md`

Maak de map `.claude` aan als die nog niet bestaat. Gebruik je zowel de globale als de projectversie, houd ze dan gelijk.

## Hoe het werkt

- Claude Code laadt `CLAUDE.md` automatisch aan het begin van elke sessie.
- `project.md` en `design.md` worden niet automatisch geladen. `CLAUDE.md` vertelt Claude Code om `/docs/project.md` te controleren en te lezen aan het begin van elke sessie, en om `/docs/design.md` te volgen bij UI-werk.
- Bij een conflict wint `project.md` voor projectspecifieke zaken. De regels over git en veiligheid blijven altijd gelden.

## Belangrijkste regels in het kort

- Antwoorden in het Nederlands. Code, comments en commit messages in het Engels.
- Alleen doen wat gevraagd is, geen ongevraagde extra's.
- De makkelijke en gebruikelijke route kiezen, zoals Docker in plaats van een losse dev-server.
- De stijl van de bestaande codebase matchen, en HTML, CSS en JS gescheiden houden.
- Eigen code zo ver mogelijk zelf testen.
- Nooit zelf committen. Bij grote features geeft Claude Code een commitplan met bestanden en commit messages, in het format van de huidige branch (bijvoorbeeld `feat(#401): added login`).

## Onderhoud

Corrigeer je Claude Code twee keer op hetzelfde punt? Zet er dan een concrete regel voor in `CLAUDE.md`. Houd het bestand kort en toetsbaar, want alles wat erin staat wordt elke sessie meegeladen.

Zet je de bestanden in een repo die je met anderen deelt, en wil je je persoonlijke regels privé houden? Voeg `.claude/` dan toe aan `.gitignore`.
