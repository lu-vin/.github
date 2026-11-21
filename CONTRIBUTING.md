# Cómo contribuir

Todos los repositorios de Luvin trabajan igual: **trunk-based development**.

1. Parte de `main` actualizado: `git switch main && git pull`.
2. Crea una rama corta para un solo cambio, de 2 días como máximo: `fix/runner-cancel`, `feat/...`, `docs/...`.
3. Cada commit sigue [Conventional Commits](https://www.conventionalcommits.org): `tipo(alcance): resumen`, por ejemplo `fix(runner): stop on cancel`. Tipos: `feat`, `fix`, `docs`, `style`, `refactor`, `perf`, `test`, `build`, `ci`, `chore`, `revert`; `!` después del tipo marca un cambio que rompe algo.
4. Abre un pull request. Cuando los checks están en verde, fusiónalo con **Rebase and merge** (el único método permitido). La rama se borra sola.

`main` no acepta push directo, push forzado ni borrado. Lo que no está terminado entra a `main` apagado (en la app, con una feature flag), nunca se queda esperando en una rama.
