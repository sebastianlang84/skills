---
name: skill-manager
description: Find, install, update, and remove Claude Code skills. Use this skill whenever the user asks whether a skill exists, wants to install/remove/update a skill, asks "gibt es einen X skill?", "kannst du X skill installieren?", "welche skills haben wir?", or wants to know what skills are available locally or on skills.sh. Always trigger this skill proactively when the user mentions skills, SKILL.md files, or asks about agent capabilities.
---

# Skill Manager

Hilft beim Finden, Installieren, Aktualisieren und Entfernen von Skills — lokal und von skills.sh.

## Skill-Architektur auf diesem System

**Primärer Store** — alle Skills leben hier, agent-agnostisch:
- **Global**: `~/.agents/skills/<skill-name>/`
- **Repo-spezifisch**: `<repo>/.agents/skills/<skill-name>/`

**Symlinks** — Agent-spezifische Einstiegspunkte zeigen auf den Store:
- `~/.claude/skills/` → `~/.agents/skills/` (Claude Code global)
- `<repo>/.claude/skills/` → `<repo>/.agents/skills/` (Claude Code repo)
- Andere Agents (Codex, Cursor, etc.) bekommen ebenfalls Symlinks in ihre Config-Ordner

**Warum so?** Skills einmal im `.agents/`-Ordner ablegen, automatisch für alle Agents verfügbar — keine Duplikate, kein Agent-Lock-in.

**Neue Skills manuell anlegen** (z.B. eigene/custom Skills):
```bash
mkdir -p ~/.agents/skills/<skill-name>
# SKILL.md dort schreiben
# Claude Code liest es sofort über den bestehenden Symlink
```

## Workflow: Skill-Anfrage beantworten

1. **Lokal prüfen**:
   ```bash
   ls ~/.agents/skills/
   ls .agents/skills/ 2>/dev/null   # nur im Repo-Kontext
   ```

2. **Eingebaute skills.sh-Tabelle** prüfen (unten) — meist reicht das.

3. **skills.sh live** — wenn Tabelle nicht ausreicht:
   - `npx skills find <stichwort>` oder Fetch `https://skills.sh/`

4. **Konkret anbieten**: Was lokal vorhanden ist, was installierbar wäre, welcher `npx`-Befehl nötig ist.

## npx skills — Cheat Sheet

### Installieren
```bash
# Einzelnen Skill global installieren (landet in ~/.agents/skills/)
npx skills add <owner/repo> -g -s <skill-name> -y

# Mehrere Skills global
npx skills add <owner/repo> -g -s skill-a -s skill-b -y

# Alle Skills eines Repos global
npx skills add <owner/repo> -g --all -y

# Repo-spezifisch (ohne -g, im Repo-Verzeichnis ausführen)
npx skills add <owner/repo> -s <skill-name> -y

# Erst auflisten ohne zu installieren
npx skills add <owner/repo> --list
```

> **Wichtig**: `--all` und `-s` schließen sich aus — `--all` überschreibt `-s`. Immer nur eines von beiden verwenden.

### Anzeigen
```bash
npx skills list              # alle installierten Skills
npx skills ls -g             # nur globale Skills
npx skills find [stichwort]  # interaktive Suche
```

### Updates
```bash
npx skills check   # verfügbare Updates anzeigen (kein Install)
npx skills update  # alle Skills aktualisieren
```

### Entfernen
```bash
npx skills remove <skill-name> -g -y        # global entfernen
npx skills remove <skill-name> -y           # repo-spezifisch entfernen
npx skills remove -g --all -y               # ALLE globalen entfernen (Vorsicht!)
```

## Bekannte Skills auf skills.sh (Top-Auswahl)

| Skill | Repo | Kategorie |
|-------|------|-----------|
| `frontend-design` | `anthropics/skills` | Frontend |
| `pdf` | `anthropics/skills` | Dokumente |
| `pptx` | `anthropics/skills` | Dokumente |
| `docx` | `anthropics/skills` | Dokumente |
| `xlsx` | `anthropics/skills` | Dokumente |
| `mcp-builder` | `anthropics/skills` | MCP/Tooling |
| `webapp-testing` | `anthropics/skills` | Testing |
| `skill-creator` | `anthropics/skills` | Meta/Skills |
| `requesting-code-review` | `obra/superpowers` | Code Review |
| `receiving-code-review` | `obra/superpowers` | Code Review |
| `systematic-debugging` | `obra/superpowers` | Debugging |
| `test-driven-development` | `obra/superpowers` | Testing |
| `verification-before-completion` | `obra/superpowers` | Qualität |
| `brainstorming` | `obra/superpowers` | Planung |
| `writing-plans` | `obra/superpowers` | Planung |
| `documentation-writer` | `github/awesome-copilot` | Doku (Diátaxis) |
| `browser-use` | `browser-use/browser-use` | Browser |
| `supabase-postgres-best-practices` | `supabase/agent-skills` | DB |
| `shadcn` | `shadcn/ui` | UI |
| `playwright-best-practices` | `currents-dev/playwright-best-practices-skill` | Testing |
| `neon-postgres` | `neondatabase/agent-skills` | DB |
| `firecrawl` | `firecrawl/cli` | Web Scraping |
| `ai-sdk` | `vercel/ai` | AI SDK |
| `better-auth-best-practices` | `better-auth/skills` | Auth |
| `find-skills` | `vercel-labs/skills` | Meta/Discovery |

Vollständige Liste: `https://skills.sh/`

## Lokal installierte Skills (Referenz)

> Immer mit `ls ~/.agents/skills/` gegenprüfen — diese Liste kann veralten.

**Global** (`~/.agents/skills/`):
- `skill-manager` ← dieser Skill (manuell, kein npx)
- `documentation-writer` (github/awesome-copilot)
- `frontend-design`, `skill-creator`, `pdf`, `pptx`, `docx`, `xlsx` u.a. (anthropics/skills)
- `brainstorming`, `requesting-code-review`, `receiving-code-review`, `systematic-debugging`,
  `verification-before-completion`, `writing-plans`, `test-driven-development` u.a. (obra/superpowers)

**Repo ai_stack** (`.agents/skills/`):
- `ai-stack-backup-restore`, `ai-stack-compose-validate`, `ai-stack-living-docs`,
  `ai-stack-openwebui-tool-imports`, `ai-stack-secrets-env-hygiene`, `ai-stack-service-scaffold`,
  `codex-mcp-self-config`, `owui-prompt-api-loop`, `owui-prompt-debug-loop`, `test-artifacts-cleanup`
