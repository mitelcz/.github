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
- **Commituje se přímo do `main`** (výjimka z pravidla pull requestů v manuálu GitHubu): kontrola pull requestů tu neběží. Zpráva commitu vysvětlí, co se mění a proč, jiný záznam o změně není.
- **Změna společného workflow platí hned pro všechny repozitáře**, které ho volají přes `@main`. Předem z větve ji ověřit nejde: GitHub App `claude` vydá token jen workflow shodnému s verzí ve výchozí větvi, takže by se kontrola v testovacím pull requestu přeskočila. Ověřuje se hned po pushi do `main` novým testovacím pull requestem v pilotním repozitáři; když selže, změnu vrať revertem. Re-run otevřeného pull requestu sice novou verzi z `main` použije také, Claude v něm ale nevidí komentáře z doby po spuštění a dřívější nálezy by zapsal znovu.
- **Kontrola pull requestů v tomto repozitáři neběží:** skupina self-hosted runnerů organizace veřejné repozitáře nepouští. Změny se kontrolují ručně.
- **Výchozí šablony** issues (`.github/ISSUE_TEMPLATE/`) a pull requestů (`.github/pull_request_template.md`) platí pro všechny repozitáře organizace, které nemají vlastní; vlastní složka `.github/ISSUE_TEMPLATE` v repozitáři přebije všechny výchozí šablony issues. Šablony odpovídají manuálu GitHubu (Issues → Struktura textu, Větve a pull requesty → Popis pull requestu) a mění se spolu s ním. Do šablony pull requestu nepatří HTML komentáře: popis pull requestu se při squash merge stane zprávou commitu v `main`.
