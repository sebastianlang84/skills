---
name: git-workflow
description: Operative Git-Workflow-Anleitung fuer ai_stack. Verwende diesen Skill bei allen Git-Entscheidungen: neuer Branch, Worktree, Commit, Merge, Push, Rebase, Sub-Agent-Isolation. Gilt fuer alle Tasks in diesem Repo.
---

# Git-Workflow — ai_stack

Operative Handlungsanleitung. Normquelle ist `AGENTS.md`; dieser Skill ergaenzt die Trigger-Matrix.

---

## Branch-Entscheidung (vor Task-Start)

**Default: direkt auf `main` arbeiten** (Trunk-Based Development, Solo-Operator-Variante).

**Neuer Branch nur wenn mindestens eines gilt:**
- parallele Tasks sollen gleichzeitig offen bleiben
- Sub-Agent arbeitet isoliert mit eigenem Schreibkontext
- High-Risk: DB-Migration, Secrets-Rotation, Netzwerk/Security-Exposure
- WIP soll ueber Session-Grenzen erhalten bleiben (kein sofortiger Merge moeglich)
- explizit experimentelle Arbeit mit wahrscheinlichem Rollback

**Branch-Namensformat:** `<type>/<scope>/<topic>`
- `type`: `fix`, `feat`, `docs`, `chore`, `ops`, `refactor`, `test`, `wip`
- Fuer `ai_stack` sind `feat/*`, `fix/*`, `wip/*` die Standardpfade; weitere Typen nur wenn sie den Task klarer benennen
- `scope`: echter Service- oder Repo-Bereichsname (z. B. `newsletter-writer`, `transcript-miner`, `repo`, `docs`)
- `topic`: 2-5 Woerter, kebab-case, konkret

Beispiele:
```
fix/newsletter-writer/delivery-lock-timeout
feat/transcript-miner/fetch-backoff
docs/repo/git-workflow-policy
chore/repo/plan-cleanup
ops/scheduler/evening-split
```

---

## Worktree-Entscheidung

**Branch im aktuellen Worktree reicht**, wenn:
- nur ein Task aktiv bearbeitet wird
- kein weiterer Branch parallel offen gehalten werden muss
- keine Sub-Agents mit eigenem Schreibkontext laufen

**Separater Worktree ist Pflicht**, wenn mindestens eines gilt:
- zwei Tasks sollen parallel offen bleiben
- Sub-Agent soll isoliert in eigenem Schreibkontext arbeiten
- temporaerer Integrationsbranch fuer Merge/Rebase/Conflict-Aufloesung noetig
- aktueller Worktree ist dirty, aber anderer Task soll parallel sauber weiterlaufen
- WIP-Kontext soll erhalten bleiben ohne sofortigen Commit

Leitregel: **Branch trennt Historie. Worktree trennt gleichzeitige Arbeitskontexte.**

---

## Commit-Trigger

**Committen**, wenn mindestens eines gilt:
- ein klarer Teil der Arbeit ist inhaltlich abgeschlossen
- der Zustand ist sinnvoll pruefbar und isolierbar
- Kontext, Task oder Arbeitsmodus wechselt danach
- Sub-Agent-Ergebnis soll integriert oder eingefroren werden

**Nicht committen** fuer:
- zufaellige Mini-Zwischenstaende ohne klare Aussage
- offensichtlich kaputte oder unzusammenhaengende Zustaende

Leitregel: **Commit on meaningful checkpoint.**

Commit-Format: `type(scope): beschreibung`
type ∈ {docs, fix, feat, chore, ops, refactor, test}

---

## Merge-Regeln

**Mergen**, wenn alles gilt:
- Arbeitseinheit auf dem Branch ist inhaltlich abgeschlossen
- noetige Verifikation ist gelaufen
- klar ist, wohin integriert wird

**Immer `--no-ff`** bei lokaler Branch-Integration nach `main`.
Kein Merge aus Neben-Kontexten direkt nach `origin/main`.

Leitregel: **Merge = abgeschlossene, verifizierte Arbeitseinheit wird integriert.**

---

## Push-Regeln

**`main` (Normalfall bei Trunk-Based):**
- Push nach jedem abgeschlossenen Task
- Push vor Session-Ende / Shutdown / Kontextwechsel
- Nur von lokalem `main`, nach lokaler Verifikation
- Nie aus Neben-Kontexten (Sub-Agent-Worktree, temporaerer Merge-Branch) nach `origin/main`

**Neben-Kontext** = jeder Git-Kontext ausser dem primaeren Arbeitszweig.

**Arbeitsbranches** (`fix/*`, `feat/*`, etc. — wenn Branch ausnahmsweise angelegt):
- push early, push often (backup-orientiert)
- Typische Trigger: nach erstem sinnvollen Commit, vor Kontextwechsel, vor Session-Ende

Leitregel: **`main`: push after task. Branches: push early.**

---

## Rebase-Regeln

**Erlaubt**, wenn:
- Branch noch nicht gepusht (kein Remote-Tracking)
- oder Branch ist gepusht, aber ausschliesslich vom eigenen Operator benutzt

Typischer Anwendungsfall: Commit-History saeubern vor Merge nach `main` (squash, fixup).

**Verboten**, wenn:
- Branch liegt in `origin` und koennte von anderen Kontexten benutzt werden
- Ziel-Branch ist `main` oder `origin/main`
- Rebase wuerde publizierte History umschreiben

Integration nach `main` immer via Merge `--no-ff`, nicht Rebase.

Leitregel: **Rebase = lokales Aufraeumen. Merge --no-ff = Integration, immer.**

---

## Sub-Agent-Isolation

**Read-only Sub-Agents:** kein eigener Worktree noetig.

**Write-capable Sub-Agents:** vor Start Scope-Pruefung:
- Welche Dateien gehoeren dem Sub-Agenten?
- Gibt es Beruehrungspunkte mit Hauptagent oder anderen Sub-Agents?

**Kein eigener Worktree noetig**, wenn:
- Write-Scope klar abgegrenzt, keine Ueberlappung, Patch klein bis mittel

**Eigener Worktree noetig**, wenn:
- Write-Scopes unklar oder potenziell ueberlappend
- Hauptagent und Sub-Agent parallel an verwandten Bereichen arbeiten
- mehrere Sub-Agents parallel schreiben

Leitregel: **Erst Scope bewerten, dann Isolationstiefe waehlen.**
