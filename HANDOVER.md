# HANDOVER.md — Session history

Newest entries at the top.

---

## 2026-03-07 — skill-context-ir: Handoff block architecture

**PR:** #28 (mergeado) + #29 (workflow.md doc, pendente)
**Branch:** worktree-skill-context-ir

### O que foi feito

Implementada a arquitetura "Option C" de handoff entre skills:
- `discovery.md` template ganhou seção `## Handoff` obrigatória com campos `carry_forward`, `excluded`, `invalidated`, `gate`
- `research.md` template ganhou seção `## Handoff` equivalente
- Phase A.1 de `start-feature --deep` atualizada: lê apenas `## Handoff` section de `discovery.md`; graceful degradation se bloco ausente
- `explore.md` (global) ganhou `## Handoff` block no template de saída
- `skill-contracts.md` (global): Lei 3 reformulada de "usuário é o gate" para "Handoff block é o contrato entre skills"
- Docs (`workflow.md`, `~/.claude/CLAUDE.md`) atualizados para refletir nova arquitetura
- Corrigido bug pré-existente: `checkpoint.md:119` excedia limite de linha (CI estava vermelho)

### Decisões tomadas

- **Option C escolhida** sobre A, B, D: path convention + leitura seletiva do `## Handoff` — menor fricção, menor acoplamento de schema, graceful degradation
- Gate humano NÃO eliminado — relocado: usuário revisa o bloco de ~10 linhas antes do `/clear`, não o artefato inteiro
- Pre-bet transition (`--discover` → bet) permanece gate humano explícito (`gate: sim`)
- `explore.md` e `skill-contracts.md` são arquivos globais (`~/.claude/`) — não versionados no kickstart repo; editados diretamente

### Armadilhas encontradas

- `worktree-skill-context-ir` não segue a convenção `feat/` do CLAUDE.md — criado pelo EnterWorktree com prefixo `worktree-`. CI aceita qualquer branch name.
- `checkpoint.md:119` era pré-existente em main mas não estava quebrando CI anteriormente (versão de markdownlint mudou)

### Próximos passos

- Merge PR #29 (workflow.md doc update)
- Testar em uso real: rodar `/start-feature --discover` e verificar que o `## Handoff` block é gerado e lido corretamente pela fase seguinte

### Arquivos-chave

- `.claude/commands/start-feature.md` — templates de discovery.md e research.md + Phase A.1
- `~/.claude/commands/explore.md` — template de explore.md
- `~/.claude/rules/skill-contracts.md` — Lei 3
- `.claude/rules/workflow.md` — princípio central

---

## 2026-02-27 — Bootstrap via /start-project

**What was done:**

- Executed Fase 3 (Bootstrap) of `/start-project` for the `claude-kickstart` template repository
- Created GitHub repo `rmolines/claude-kickstart` (public)
- Wrote all project files: CLAUDE.md, Makefile, CI workflows, skills, hooks, rules, memory files

**Architectural decisions:**

- GitHub Template Repository format (not CLI) — zero friction
- Hooks in `.claude/hooks/` external scripts (not inline `settings.json`) — auditable, CVE-2025-59536 compliant
- Static CI only (lint + JSON + structure) — no runtime to test
- `bootstrap.yml` with `run_number == 1` guard — auto-applies branch protection on first fork push

**Files created:**

- `CLAUDE.md`, `README.md`, `LEARNINGS.md`, `HANDOVER.md`, `Makefile`
- `.claude/settings.json`, `.claude/settings.md`
- `.claude/hooks/pre-tool-use.sh`
- `.claude/scripts/validate-structure.sh`
- `.claude/rules/git-workflow.md`, `coding-style.md`, `security.md`
- `.claude/commands/start-feature.md`, `ship-feature.md`, `close-feature.md`, `handover.md`, `sync-skills.md`
- `.claude/commands/SYNC_VERSION`
- `.github/workflows/ci.yml`, `bootstrap.yml`, `template-sync.yml`
- `.github/dependabot.yml`, `CODEOWNERS`, `SECURITY.md`
- `memory/MEMORY.md`

**Open threads:**

- Demo GIF/video for README (identified as high-risk if not done before launch)
- CONTRIBUTING.md for community contributors
- Mark repo as Template in GitHub Settings (done via API in bootstrap sequence)
