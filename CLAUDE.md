# CLAUDE.md

Tento soubor poskytuje společné projektové pokyny pro Claude Code, Codex a další AI coding agenty při práci s kódem v tomto repozitáři.

Agenti jiní než Claude Code (Codex apod.): pokud existuje soubor `CLAUDE.local.md`, přečti si ho hned na začátku session - obsahuje osobní/lokální poznámky pro tento stroj. (Claude Code ho načítá automaticky.)

Repozitář `.github` organizace `mitelcz` drží společná GitHub workflow (automatická kontrola pull requestů) a výchozí soubory organizace. Popis pro lidi je v `README.md`.

## Firemní dokumentace

Zdrojem pravdy pro všechno, co přesahuje kód tohoto repozitáře (infrastruktura, nasazení a provoz, pravidla vývoje, práce s GitHubem: issues, commity, větve a pull requesty), je firemní dokumentace v repozitáři `mitelcz/docs` na GitHubu. Před prací, která se takových věcí týká, si přečti příslušné stránky, vždy aktuální verzi z GitHubu, ne lokální klon:

- mapa stránek: sekce `nav` v `gh api repos/mitelcz/docs/contents/mkdocs.yml -H "Accept: application/vnd.github.raw"`
- stránka: `gh api repos/mitelcz/docs/contents/docs/<cesta>.md -H "Accept: application/vnd.github.raw"`

Když se dokumentace a skutečnost rozcházejí, upozorni na to uživatele.

## Pravidla repozitáře

- **Repozitář je veřejný.** Nepatří do něj nic tajného ani interního: žádné secrety, IP adresy, názvy serverů, interní adresy ani popis sítě. Identifikátory federace ve workflow (organizace, workspace, pravidlo) tajné nejsou.
- **Změna společného workflow platí hned pro všechny repozitáře**, které ho volají přes `@main`. Předem z větve ji ověřit nejde: GitHub App `claude` vydá token jen workflow shodnému s verzí ve výchozí větvi, takže by se kontrola v testovacím pull requestu přeskočila. Ověřuje se hned po sloučení znovuspuštěním kontroly (Re-run) v otevřeném testovacím pull requestu, které už použije novou verzi z `main`; když selže, změnu vrať revertem.
- **Kontrola pull requestů v tomto repozitáři neběží:** skupina self-hosted runnerů organizace veřejné repozitáře nepouští. Změny se kontrolují ručně.
- **Výchozí šablony a profil organizace** (`PULL_REQUEST_TEMPLATE.md`, `ISSUE_TEMPLATE/`, `profile/README.md`) platí pro všechny repozitáře organizace, které nemají vlastní, profil pro veřejnou stránku organizace.
