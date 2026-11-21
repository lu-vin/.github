# lu-vin/.github

La forma de trabajar de todos los repositorios de Luvin, en un solo lugar:

- **Workflows compartidos** (`.github/workflows/`), que cada repositorio llama con una línea.
- **Archivos por defecto** de la organización: [plantilla de pull request](PULL_REQUEST_TEMPLATE.md) y [guía para contribuir](CONTRIBUTING.md). GitHub los usa en todo repositorio que no tenga los suyos.

Las reglas (`main` protegido, solo rebase, Conventional Commits, tags `v*` protegidos) son **rulesets de la organización**: aplican solas a todo repositorio, incluido uno nuevo.

Las decisiones y sus razones están en `luvin-architecture`, `deployment-architecture/0004-org-standards-shared-workflows-templates`.

## Workflows compartidos

| Workflow | Qué hace | Check que produce |
|---|---|---|
| [`commits.yml`](.github/workflows/commits.yml) | Cada commit del pull request sigue Conventional Commits | `commits / Conventional Commits` (exigido en toda la organización) |
| [`site.yml`](.github/workflows/site.yml) | Sitio Astro: compila, corre `npm run verify` y, en `main`, publica en Cloudflare Workers | `site / build`, `site / deploy` |
| [`docs.yml`](.github/workflows/docs.yml) | Repositorio de documentos: enlaces rotos entre archivos Markdown | `docs / links` |
| [`branches.yml`](.github/workflows/branches.yml) | Cada semana, un issue con las ramas de más de 2 días | (programado, no es un check) |

Se usan desde `main` (`@main`): un cambio aquí llega a todos los repositorios en su siguiente ejecución. Por eso este repositorio revisa sus propios workflows (actionlint) y los usa en su CI antes de fusionar.

## Crear un repositorio nuevo

| Si es… | Template |
|---|---|
| Un sitio (Astro en Cloudflare Workers) | [`lu-vin/template-web`](https://github.com/lu-vin/template-web) |
| Documentos y diagramas | [`lu-vin/template-docs`](https://github.com/lu-vin/template-docs) |
| Otra cosa (una app, un servicio) | Ninguno todavía: parte de cero y llama a `commits.yml` y `branches.yml` |

> **Todo repositorio necesita el check `commits / Conventional Commits`.** El ruleset de la organización lo exige en el `main` de todos los repositorios, incluidos los nuevos. Un repositorio sin un job `commits` que llame a `commits.yml` no puede fusionar ningún pull request. Los templates ya lo traen; si partes de cero, agrégalo antes del primer pull request:
>
> ```yaml
> jobs:
>   commits:
>     if: github.event_name == 'pull_request'
>     uses: lu-vin/.github/.github/workflows/commits.yml@main
> ```

```sh
gh repo create lu-vin/<nombre> --private --template lu-vin/template-web --clone
gh repo edit lu-vin/<nombre> --enable-rebase-merge --enable-merge-commit=false --enable-squash-merge=false --delete-branch-on-merge
```

El segundo comando deja los botones de merge como exige el ruleset (solo rebase) y borra las ramas al fusionar; un template no copia la configuración del repositorio. Cada template explica en su README lo que falta (secretos, dominio).

## Cambiar un workflow compartido

1. Rama corta y pull request, como en cualquier repositorio.
2. El CI corre actionlint y usa los workflows cambiados en este mismo repositorio.
3. Si cambia un input o el nombre de un job, cambia también el nombre del check: actualiza primero los rulesets que lo exigen.
