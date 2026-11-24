# .github

Společná GitHub workflow a výchozí soubory organizace [MITEL a.s.](https://github.com/mitelcz) na GitHubu.

## Obsah

- `.claude/settings.json` - nastavení Claude Code: mapa firemní dokumentace do kontextu při startu session (hook).
- `.github/ISSUE_TEMPLATE/` - výchozí formuláře issues: pro každý typ issue (Task, Bug, Feature, Decision) jeden, s oddíly podle manuálu GitHubu; prázdné issue (Blank issue) web nabízí jen lidem s právem zápisu, s označením Maintainers only.
- `.github/workflows/code-review.yml` - automatická kontrola kódu pull requestů nástrojem Claude, společná pro všechny repozitáře organizace; repozitáře ji volají jako úlohu svého CI.
- `.github/pull_request_template.md` - výchozí osnova popisu pull requestu.

Výchozí šablony platí pro všechny repozitáře organizace, které nemají vlastní.

## Automatická kontrola pull requestů

Workflow `code-review.yml` zkontroluje pull request jako úloha CI repozitáře, až po průchodu bránou kvality (lint, testy atd.), takže se neplatí kontrola kódu, který bránou neprošel. Nálezy zapíše jako komentáře u řádků a průběžně aktualizovaný souhrnný komentář pod účtem `claude[bot]`. Kontroluje jen pull requesty, drafty přeskakuje (kontrola proběhne po převodu na Ready for review). Pull request neschvaluje. Workflow má dvě úlohy:

- **Analysis** pull request zkontroluje. Check je červený jen tehdy, když kontrola neproběhla (přeskočila se nebo nedoběhla).
- **Findings** je červený, dokud zůstává nevyřešená konverzace u nálezu kontroly. Opravený nález i nález zdůvodněný v odpovědi se označí Resolve conversation a úloha se spustí znovu (Re-run failed jobs). Úloha nevolá Claude API, kontrola Claudem se při tom neopakuje.

Provoz:

- Běží na self-hosted runneru organizace (label `coolify`), ne na runnerech GitHubu, takže nečerpá minuty GitHub Actions.
- Do Claude API se přihlašuje přes Workload Identity Federation: OIDC token z GitHubu se vymění za krátkodobý token Claude API. V repozitářích proto není žádný secret a identifikátory federace ve workflow nejsou tajné.
- V pull requestu, který mění `ci.yml`, se kontrola přeskočí a check Analysis je červený: GitHub App `claude` vydá token jen workflow shodnému s výchozí větví.

Repozitář kontrolu zapne úlohou `code-review` ve svém `.github/workflows/ci.yml`, která čeká na úlohy brány kvality (`needs`):

```yaml
# Brána kvality z CLAUDE.md a automatická kontrola kódu pull requestu.
name: CI
on:
  pull_request:
    types: [opened, synchronize, reopened, ready_for_review]
# Nový push do pull requestu zruší celý běh předchozího commitu.
concurrency:
  group: ci-${{ github.event.pull_request.number }}
  cancel-in-progress: true
jobs:
  # Brána kvality: lint, testy atd. podle projektu.
  quality-gate:
    name: Quality gate
    runs-on: [self-hosted, coolify]
    permissions:
      contents: read
    steps:
      - uses: actions/checkout@v7
      # kroky brány kvality projektu
  # Automatická kontrola kódu, společný workflow z repozitáře mitelcz/.github.
  # Běží až po průchodu bránou kvality, kód, který jí neprošel, nemá smysl kontrolovat.
  code-review:
    name: Code review
    needs: quality-gate
    uses: mitelcz/.github/.github/workflows/code-review.yml@main
    # Aspoň oprávnění, která požadují úlohy společného workflow, jinak se běh vůbec nespustí.
    permissions:
      contents: read
      pull-requests: write
      id-token: write
```

Repozitář bez brány kvality má jen úlohu `code-review` bez `needs`. V pull requestu a v Actions se kontrola zobrazuje jako checky „CI / Code review / Analysis" a „CI / Code review / Findings". Logika kontroly (runner, přihlášení, prompt, model, limity) je jen ve společném workflow, změna v něm platí pro všechny repozitáře najednou. Pravidla práce s nálezy jsou v [manuálu GitHubu](https://docs.mitel.cz/aplikace/github/) ve firemní dokumentaci.

## Práce s backlogem

Úkoly, chyby a rozhodnutí se evidují v [GitHub Issues](https://github.com/mitelcz/.github/issues) tohoto repozitáře. Pravidla jsou společná pro všechny repozitáře organizace: [manuál GitHubu](https://docs.mitel.cz/aplikace/github/) ve firemní dokumentaci. Specifika tohoto repozitáře:

- **Veřejný repozitář:** na rozdíl od ostatních repozitářů organizace je veřejný, protože výchozí šablony issues a pull requestů bere GitHub jen z veřejného repozitáře `.github`. Nepatří do něj nic tajného ani interního.
- **Přímo do `main`:** repozitář je výjimkou z pravidla pull requestů, commituje se přímo do `main`. Automatická kontrola pull requestů v něm neběží.
- **Labels:** zatím žádné, repozitář má jen pár souborů.
- **Milestones:** repozitář nemá fáze, milestones nepoužívá.
