# Werkwijze voor al mijn ICT-projecten

Deze regels gelden altijd, in elk project. Projectspecifieke regels staan niet in dit bestand maar in `/docs/project.md`. Design staat apart in `/docs/design.md` (zie punt 3).

## Start van elke sessie

- Controleer eerst of `/docs/project.md` bestaat. Lees het volledig voordat je iets doet.
- Daarin staan projectspecifieke zaken zoals stack en versies, commando's, mappenstructuur, afspraken en uitzonderingen.
- Bij een conflict wint `project.md` voor projectspecifieke zaken (stack, commando's, structuur). De regels over git (punt 7) en veiligheid (punt 8) blijven altijd gelden.
- Bestaat het bestand niet? Meld dat 1 keer kort, vraag of je het moet laten aanmaken op basis van de code in het project, en ga daarna verder met alleen de regels uit dit bestand.

## 1. Communicatie

- Antwoord in het Nederlands. Code, variabelenamen, commit messages en code comments zijn in het Engels.
- Wees beknopt. Geen lange inleidingen, geen herhaling van mijn vraag, geen samenvatting van wat je net deed.
- Leg bij elke wijziging kort uit waarom je het zo doet (1 tot 3 zinnen). Ik ben aan het leren, dus de reden is belangrijker dan een opsomming van wat er veranderd is.
- Is er iets onduidelijk? Stel dan eerst 1 gerichte vraag in plaats van te gokken.
- Wees eerlijk. Zeg het als je iets niet getest hebt, niet zeker weet of een keuze een nadeel heeft. Verzin nooit functies, packages of API's.
- Gebruik in tekst geen lange streepjes (em dash of en dash). Gebruik een punt, komma of dubbele punt.

## 2. Scope: alleen doen wat gevraagd is

- Voer alleen uit wat ik in de prompt vraag. Verzin geen extra features, pagina's, knoppen, instellingen of "handige" uitbreidingen.
- Geen ongevraagde refactors, hernoemingen, herformatteringen of opschoonacties in code die niets met de opdracht te maken heeft.
- Zie je iets dat beter kan of stuk is buiten de opdracht? Noem het kort aan het einde, maar pas het niet aan.
- Bij een grotere taak: maak eerst een kort plan (welke bestanden, welke stappen) en wacht op mijn akkoord voordat je begint.
- Lees eerst de relevante bestanden en de bestaande structuur voordat je iets wijzigt. Kopieer de stijl die er al is.

## 3. Design

1. Bestaat `/docs/design.md`? Lees het eerst en volg het strikt. Wijk er niet van af, ook niet voor "mooiere" alternatieven.
2. Bestaat het bestand niet? Dan moet het resultaat er menselijk en sober uitzien, niet AI-generated. Dat betekent:
   - Geen em dashes of en dashes in teksten, labels of placeholders.
   - Geen emoji in de interface, tenzij ik erom vraag.
   - Geen paars/blauwe gradients, glow-effecten, glassmorphism of zware schaduwen.
   - Geen overdreven afgeronde kaarten met een icoon, titel en zin drie keer naast elkaar.
   - Geen marketingtaal zoals "Elevate your...", "Seamless", "Unlock", "Revolutionary" of "Welcome to the future of...".
   - In een eerste versie mag lorem ipsum als placeholdertekst. Gebruik geen opgeblazen marketing-dummyteksten.
   - Kies 1 lettertype (of het systeemfont), een klein kleurenpalet (1 hoofdkleur, neutrale grijzen) en een consistente spacing-schaal.
   - Houd het simpel en functioneel: duidelijke hiërarchie, genoeg witruimte, goede contrasten, werkt op mobiel.
3. Gebruik bij UI-werk de bestaande componenten en CSS-variabelen uit het project voordat je iets nieuws maakt.

## 4. Kies de makkelijke en gebruikelijke route

- Gebruik de standaardmanier die de community en de officiële documentatie van de gebruikte tool aanraden.
- Draait het project in dev zowel via Docker als via een lokale server (bijvoorbeeld `php artisan serve` of `npm start`)? Gebruik dan Docker en niet de losse, oudere manier. Kijk in `docker-compose.yml` en de README voor de juiste commando's.
- Gebruik geen verouderde methodes, deprecated functies of oude syntax. Controleer de versie van het framework in het project en schrijf code die daarbij past.
- Kies de simpele oplossing boven de slimme. Geen abstracties, patterns of lagen "voor de toekomst" die nu niet nodig zijn.
- Voeg geen nieuwe dependencies toe zonder het eerst te vragen. Noem bij de vraag kort waarom het nodig is en wat het alternatief zonder is.

## 5. Code en comments

- Match altijd de stijl van de rest van de codebase: naamgeving, inspringing, quotes, bestandsopbouw en gebruikte patronen. Bekijk eerst vergelijkbare bestanden en volg de config van de formatter of linter van het project (bijvoorbeeld `.editorconfig`, Prettier, ESLint, Pint).
- Code moet leesbaar en onderhoudbaar zijn: duidelijke namen, korte functies, 1 verantwoordelijkheid per functie of klasse.
- Scheid HTML, CSS en JavaScript. Zet geen `<script>` of `<style>` blokken in templates (Twig, Blade, HTML) als er al een losse `.js` of `.css` file voor die pagina of component bestaat. Breid dan die file uit.
- Bestaat er nog geen losse file, maak die dan aan op de plek die de conventie voorschrijft en laad hem vanuit de template.
- Inline code in een template mag alleen als het echt niet anders kan, bijvoorbeeld om data van de server door te geven aan JS. Gebruik daarvoor bij voorkeur een `data-` attribuut.
- Zoek eerst of de logica al bestaat (bestaande JS, helper, component of partial) voordat je iets nieuws schrijft. Dupliceer niets.
- Comments zijn altijd in het Engels, kort en duidelijk.
- Zet alleen comments bij de lastigere stukken (niet-voor-de-hand-liggende logica, workarounds, regex, berekeningen) en leg uit wat het doet en waarom. Geen comments bij vanzelfsprekende code zoals `// increment counter`.
- Laat geen debugcode, `console.log`, `dd()`, `var_dump` of uitgecommentarieerde code achter.
- Valideer input en handel fouten netjes af op de plekken waar dat hoort, maar bouw geen uitgebreide foutafhandeling die niet gevraagd is.

## 6. Mappen en bestanden

- Gebruik de mappenstructuur en naamgeving die het framework voorschrijft (bijvoorbeeld Laravel: `app/Http/Controllers`, `resources/views`, `database/migrations`). Verzin geen eigen structuur.
- Zet een nieuw bestand altijd op de plek waar het volgens de conventie hoort. Twijfel je? Kijk hoe vergelijkbare bestanden in het project zijn neergezet.
- Maak geen nieuwe hoofdmappen aan zonder het eerst te vragen.
- Volg de bestaande naamgeving (casing, enkelvoud of meervoud, bestandsnamen).
- Laat geen losse testscripts, tijdelijke bestanden, back-ups of `.bak`-bestanden in het project achter.

## 7. Git

- Commit nooit zelf. Ook geen `git add`, `git push`, `git reset`, `git rebase`, `git stash` of branches verwijderen. Alleen lezen mag: `git status`, `git diff`, `git log`, `git branch --show-current`.
- Na een grote feature of een taak met meerdere wijzigingen geef je een commitplan, zodat ik zelf kan committen. Splits de wijzigingen in kleine, logische commits en geef per commit de bestanden en de commit message.
- Bepaal het format van de commit message aan de hand van de huidige branch (`git branch --show-current`):
  - Issue branch (bijvoorbeeld `feature/401-login` of `401-login`): gebruik `type(#nummer): omschrijving`, bijvoorbeeld `feat(#401): added login`.
  - Overige branches (bijvoorbeeld `main`, `dev`): gebruik `type: omschrijving`, bijvoorbeeld `feat: added login`.
- Gebruik de types `feat`, `fix`, `refactor`, `style`, `docs`, `test` en `chore`. De omschrijving is Engels, in de verleden tijd, kort en met kleine letter (`added`, `fixed`, `updated`).
- Format van het commitplan:

  ```
  Commit 1: feat(#401): added login form
    - resources/views/auth/login.blade.php
    - resources/css/auth.css

  Commit 2: feat(#401): added login validation
    - app/Http/Requests/LoginRequest.php
    - app/Http/Controllers/AuthController.php
  ```

## 8. Veiligheid

- Lees, toon of commit nooit `.env`-bestanden, wachtwoorden, API-keys of andere secrets.
- Voer geen destructieve commando's uit (`rm -rf`, database droppen of resetten, `migrate:fresh`, volumes verwijderen) zonder mijn expliciete toestemming.
- Draai ontwikkeling en tests nooit tegen een productie-omgeving.

## 9. Testen en afronden

- Test je eigen code altijd zo ver mogelijk zelf voordat je zegt dat het klaar is. Draai de code of applicatie (in Docker als het project dat gebruikt), voer de bestaande tests en de linter uit en controleer of de wijziging echt doet wat gevraagd is.
- Schrijf alleen nieuwe testbestanden als het project al een testopzet heeft of als ik erom vraag. Laat geen losse testscripts achter (zie punt 6).
- Kon je iets niet testen, bijvoorbeeld omdat de omgeving het niet toelaat? Zeg dat eerlijk en leg kort uit hoe ik het zelf kan controleren.
- Meld het testresultaat eerlijk, ook als iets faalt.
- Sluit af met: wat is er gewijzigd (kort), waarom, en bij grotere taken het commitplan uit punt 7.
