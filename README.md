# .github

Společná GitHub workflow a výchozí soubory organizace [MITEL a.s.](https://github.com/mitelcz) na GitHubu.

## Obsah

- `.github/workflows/kontrola-pr.yml` - automatická kontrola pull requestů nástrojem Claude, společná pro všechny repozitáře organizace.

## Automatická kontrola pull requestů

Workflow zkontroluje pull request při otevření, při každém pushi, při znovuotevření a při převodu z draftu. Nálezy zapíše jako komentáře u řádků a průběžně aktualizovaný souhrnný komentář pod účtem `claude[bot]`. Drafty přeskakuje a nový push zruší rozběhnutou kontrolu předchozího commitu. Pull request neschvaluje ani neblokuje.

- Běží na self-hosted runneru organizace (label `coolify`), ne na runnerech GitHubu, takže nečerpá minuty GitHub Actions.
- Do Claude API se přihlašuje přes Workload Identity Federation: OIDC token z GitHubu se vymění za krátkodobý token Claude API. V repozitářích proto není žádný secret a identifikátory federace ve workflow nejsou tajné.

Repozitář kontrolu zapne souborem `.github/workflows/kontrola-pr.yml`:

```yaml
name: Kontrola PR
on:
  pull_request:
    types: [opened, synchronize, reopened, ready_for_review]
jobs:
  kontrola:
    uses: mitelcz/.github/.github/workflows/kontrola-pr.yml@main
    permissions:
      contents: read
      pull-requests: write
      id-token: write
```

Logika kontroly (runner, přihlášení, prompt, model, limity) je jen ve společném workflow, změna v něm platí pro všechny repozitáře najednou. Pravidla práce s nálezy jsou v [manuálu GitHubu](https://docs.mitel.cz/aplikace/github/) ve firemní dokumentaci.

## Práce s backlogem

Úkoly, chyby a rozhodnutí se evidují v [GitHub Issues](https://github.com/mitelcz/.github/issues) tohoto repozitáře, změny jdou přes pull requesty. Pravidla jsou společná pro všechny repozitáře organizace: [manuál GitHubu](https://docs.mitel.cz/aplikace/github/) ve firemní dokumentaci. Specifika tohoto repozitáře:

- **Veřejný repozitář:** na rozdíl od ostatních repozitářů organizace je veřejný, aby fungovaly výchozí šablony a profil organizace. Nepatří do něj nic tajného ani interního.
- **Labels:** zatím žádné, repozitář má jen pár souborů.
- **Milestones:** repozitář nemá fáze, milestones nepoužívá.
