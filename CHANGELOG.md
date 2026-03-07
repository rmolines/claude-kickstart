# Changelog

---

## [feat] Handoff block architecture for skill context IR — 2026-03-07

**Tipo:** feat
**Tags:** skills, handoff, context-engineering, workflow
**PR:** [#28](https://github.com/rmolines/claude-kickstart/pull/28) · **Complexidade:** média

### O que mudou

Skills agora geram um `## Handoff` block estruturado ao final de cada artefato (`discovery.md`, `research.md`, `explore.md`).
A fase seguinte lê apenas esse bloco por path convention — em vez de consumir o artefato inteiro ou exigir copy-paste manual.

### Detalhes técnicos

- `start-feature.md` Phase 0.6: template de `discovery.md` ganha `## Handoff` com campos `carry_forward`, `excluded`, `invalidated`, `gate`
- `start-feature.md` Phase A.4: template de `research.md` ganha `## Handoff` equivalente
- `start-feature.md` Phase A.1: instrução atualizada para leitura seletiva do `## Handoff` section; graceful degradation se bloco ausente
- `explore.md` (global): `## Handoff` adicionado ao formato de saída
- `skill-contracts.md` (global): Lei 3 reformulada — "Handoff block é o contrato entre skills"
- Bug fix: `checkpoint.md:119` linha excedia 200 chars (CI estava vermelho em main)

### Impacto

- **Breaking:** Não — artefatos existentes sem `## Handoff` têm graceful degradation (leitura do arquivo inteiro + warning)

### Arquivos-chave

- `.claude/commands/start-feature.md` — templates + Phase A.1
- `~/.claude/commands/explore.md` — template de explore.md (global)
- `~/.claude/rules/skill-contracts.md` — Lei 3 (global)

---
