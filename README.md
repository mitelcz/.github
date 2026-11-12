# .github

Společná GitHub workflow a výchozí soubory organizace [MITEL a.s.](https://github.com/mitelcz) na GitHubu.

## Obsah

- `.github/ISSUE_TEMPLATE/` - výchozí formuláře issues: pro každý typ issue (Task, Bug, Feature, Decision) jeden, s oddíly podle manuálu GitHubu; prázdné issue web nenabízí.
- `.github/workflows/check-pr.yml` - automatická kontrola pull requestů nástrojem Claude, společná pro všechny repozitáře organizace.
- `.github/pull_request_template.md` - výchozí osnova popisu pull requestu.

Výchozí šablony platí pro všechny repozitáře organizace, které nemají vlastní.

## Automatická kontrola pull requestů

Workflow zkontroluje pull request při otevření, při každém pushi, při znovuotevření a při převodu z draftu. Nálezy zapíše jako komentáře u řádků a průběžně aktualizovaný souhrnný komentář pod účtem `claude[bot]`. Drafty přeskakuje a nový push zruší rozběhnutou kontrolu předchozího commitu. Pull request neschvaluje. Workflow má dvě úlohy:

- **Claude** pull request zkontroluje. Check je červený jen tehdy, když kontrola neproběhla (přeskočila se nebo nedoběhla).
- **Findings** je červený, dokud zůstává nevyřešená konverzace u nálezu kontroly. Opravený nález i nález zdůvodněný v odpovědi se označí Resolve conversation a úloha se spustí znovu (Re-run failed jobs). Úloha nevolá Claude API, kontrola Claudem se při tom neopakuje.

Provoz:

- Běží na self-hosted runneru organizace (label `coolify`), ne na runnerech GitHubu, takže nečerpá minuty GitHub Actions.
- Do Claude API se přihlašuje přes Workload Identity Federation: OIDC token z GitHubu se vymění za krátkodobý token Claude API. V repozitářích proto není žádný secret a identifikátory federace ve workflow nejsou tajné.

Repozitář kontrolu zapne souborem `.github/workflows/check-pr.yml`:

```yaml
# Automatická kontrola pull requestů, společný workflow v repozitáři mitelcz/.github.
name: Check PR
on:
  pull_request:
    types: [opened, synchronize, reopened, ready_for_review]
jobs:
  check:
    name: Check
    uses: mitelcz/.github/.github/workflows/check-pr.yml@main
    permissions:
      contents: read
      pull-requests: write
      id-token: write
```

V pull requestu a v Actions se kontrola zobrazuje jako checky „Check PR / Check / Claude" a „Check PR / Check / Findings". Logika kontroly (runner, přihlášení, prompt, model, limity) je jen ve společném workflow, změna v něm platí pro všechny repozitáře najednou. Pravidla práce s nálezy jsou v [manuálu GitHubu](https://docs.mitel.cz/aplikace/github/) ve firemní dokumentaci.

## Práce s backlogem

Úkoly, chyby a rozhodnutí se evidují v [GitHub Issues](https://github.com/mitelcz/.github/issues) tohoto repozitáře, změny jdou přes pull requesty. Pravidla jsou společná pro všechny repozitáře organizace: [manuál GitHubu](https://docs.mitel.cz/aplikace/github/) ve firemní dokumentaci. Specifika tohoto repozitáře:

- **Veřejný repozitář:** na rozdíl od ostatních repozitářů organizace je veřejný, protože výchozí šablony issues a pull requestů bere GitHub jen z veřejného repozitáře `.github`. Nepatří do něj nic tajného ani interního.
- **Labels:** zatím žádné, repozitář má jen pár souborů.
- **Milestones:** repozitář nemá fáze, milestones nepoužívá.
