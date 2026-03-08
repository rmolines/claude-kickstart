# HANDOVER.md — Session history

Newest entries at the top.

---

## 2026-03-08 — claude-code-sales-pitch

**O que foi feito:** Criada a skill `/sales-pitch` que lê os arquivos do projeto (CLAUDE.md, backlog.json, LEARNINGS.md, memory/MEMORY.md, README.md) e gera um pitch customizado por audiência (business/tech/cto). Fallback interativo com ≤ 3 perguntas se arquivos estiverem ausentes. Output apenas na conversa.

**Decisões tomadas:**
- Output conversacional apenas (sem escrita em disco) — alinhado com project-compass como referência
- Dispatch table explícita para audiências antes de qualquer lógica (CLAUDE.md pitfall: cascade conditionals)
- Fallback unificado: duas condições (3+ arquivos ausentes; CLAUDE.md com TODO intacto) avaliadas juntas
- Retorno explícito a Fase 2 após Fase 3 para garantir que audiência seja detectada mesmo no fallback

**Armadilhas encontradas:**
- Fase 3 inicialmente sem return arc para Fase 2 — pitch seria gerado sem audiência confirmada
- Dispatch table tinha `ou vazio (após perguntar)` implicando default para Modo A, mas Fase 2 não tem default
- Seção "Onde estamos" (Modo B) sem guard para backlog.json ausente — vetor de alucinação

**Próximos passos:** Nenhum imediato. Possível v2: suporte a audiência `investors` com framing de produto/SaaS.

**Arquivos-chave:** `.claude/commands/sales-pitch.md`

---

## pitadas-de-opus — 2026-03-08

**O que foi feito:** Criada a skill `/pitadas-de-opus` — protocolo de calibração deliberada para uso intencional do Opus.
3 modos: decisão de ativação (4 critérios de assimetria de risco), estrutura de sessão Opus (`--session`),
e registro acumulável em `OPUS_LOG.md` (`--log`). PR #34 mergeado em 2026-03-08.

**Decisões tomadas:**

- **7 domínios em vez de só tech:** caso de uso real são projetos multi-facetados — expandido para tech, produto, UX,
  negócios, marketing, teoria, outro; o protocolo adversarial é o mesmo para todos
- **OPUS_LOG.md na raiz, não em `.claude/`:** o log é um artefato de projeto visível (não infraestrutura interna), então fica junto de LEARNINGS.md e HANDOVER.md
- **Dispatch table explícita para flags:** `--session` e `--log` são dispatched antes do Modo 1 para evitar ambiguidade de invocação — garante que a skill nunca mistura modos
- **opus-session.md por sessão em `.claude/feature-plans/<nome>/`:** consistente com a convenção de artefatos das outras skills do projeto
- **Formato markdown heading em OPUS_LOG.md (não YAML):** legível sem ferramentas, difável, acumulável sem parser especial

**Armadilhas encontradas:**

- **MD041 com frontmatter YAML:** markdownlint exige que o primeiro heading seja `# título` — o heading deve vir logo após o bloco `---`, sem prose intermediária
- **MD013 em tabelas e prose longa:** tabelas e code blocks são exempt do limite de 200 chars, mas prose de critérios detalhados precisou ser quebrada em listas para não exceder o limite

**Arquivos-chave:**

- `.claude/commands/pitadas-de-opus.md` — skill principal com os 3 modos e dispatch table

**Próximos passos:**

- Calibrar os critérios de ativação do Modo 1 com dados reais do `OPUS_LOG.md` após algumas sessões de uso
- Considerar um `/project-compass` que leia `OPUS_LOG.md` e mostre padrões de uso (domínios mais ativados, taxa de confirmação de delta)
- Avaliar se faz sentido adicionar um `--review` mode para comparar o output da sessão Opus com a hipótese inicial registrada no log

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
