# LEARNINGS.md — Technical learnings

Gotchas, limitations, and non-obvious behaviors discovered while working on this project.

---

## GitHub Actions

### `bootstrap.yml`: `run_number == 1` guard

`github.run_number` starts at 1 for the first run of any workflow in a repo. Using this as a
guard ensures branch protection is only applied once. **Do not re-run this workflow manually** —
it will attempt to apply protection again (which is usually fine but clutters logs).

### `template-sync.yml`: must guard with `!is_template`

Without the `!github.event.repository.is_template` guard, the sync workflow would run on the
template repo itself and open PRs against its own `main`. The guard makes it a no-op on the
template and active only on forks.

### Action SHA pinning

Always pin to full commit SHA, not tag:
```yaml
# Good
uses: actions/checkout@11bd71901bbe5b1630ceea73d27597364c9af683 # v4.2.2
# Bad (tag can be hijacked)
uses: actions/checkout@v4
```

---

## Claude Code hooks (CVE-2025-59536)

Hooks in `.claude/settings.json` execute shell commands **without user confirmation**.
This was documented in CVE-2025-59536. Mitigation: keep hook logic in external scripts
(`.claude/hooks/`) so they're visible, auditable, and can be reviewed in PRs.

---

## markdownlint

- Use `npx --yes markdownlint-cli2` to avoid requiring global install
- `MD013` (line length) needs `tables: false` and `code_blocks: false` to avoid false positives
- `MD024` (duplicate headings) should be disabled for `HANDOVER.md` — entries often have similar structure
- `MD041` (first heading must be h1) breaks templates with frontmatter or `<!-- TODO -->` comments

---

## 2026-03-08 — YAML frontmatter em `.md` implica parseabilidade que não existe

Usar YAML frontmatter em `.md` de log implica parseabilidade que não existe — nenhum tooling do projeto lê esses blocos.
Preferir headings markdown (`## YYYY-MM-DD`) para registros acumuláveis: mais honesto e igualmente legível.

## 2026-03-08 — Dispatch table para parsing de flags CLI em skills markdown

Ao escrever skills com múltiplas flags (`--log`, `--session`, `--review`), condicionar em cascade captura flags indevidamente.
Solução: dispatch table explícita mapeando cada flag a exatamente um modo, avaliada antes de qualquer lógica.

## 2026-03-08 — `/cost` no Claude Code retorna custo incremental da sessão

O comando `/cost` dentro do Claude Code mostra o custo acumulado da sessão corrente (incremental, não total histórico).
Útil para medir o custo real de sessões com modelos premium (ex: Opus) e calibrar quando o uso está justificado.

## 2026-03-08 — Subagentes de review paralelo capturam issues que testes manuais perdem

Em features de skill/protocolo, rodar subagentes de review em paralelo (reuse, qualidade, eficiência) capturou 9 issues
que não apareceram em testes manuais. Padrão especialmente eficaz para skills com múltiplos modos de execução.
