# Git Pre-Read Diagnostics

> Five git log commands that diagnose a codebase before you open a single file.

Derived from [Ally Piechowski's article](https://piechowski.io/post/git-commands-before-reading-code/).

---

## Install

### opencode users

```bash
npx skills add jsfaint/git-pre-read-diagnostics
```

### Other agents

Copy `skills/git-pre-read-diagnostics/SKILL.md` to your agent's skills directory.

---

## Usage

When this skill is active, your agent runs 5 diagnostic commands on new projects and produces a codebase health report.

**Note:** Run from `app/` or `src/` (not repo root) to avoid lockfiles and generated code dominating results.

---

## The Five Diagnostics

### 1. Code Churn Hotspots

```bash
git log --format=format: --name-only --since="1 year ago" | sort | uniq -c | sort -nr | head -20
```

Top 20 most-changed files in the past year. The #1 file is usually the one everyone warns about.

Cross-reference with #3 — files on both lists keep breaking and never get properly fixed.

### 2. Bus Factor & Contributor Map

```bash
git shortlog -sn --no-merges
```

All contributors ranked by commit count. ≥60% from one person = your bus factor. Worse if they left 6 months ago.

Also check recent activity:

```bash
git shortlog -sn --no-merges --since="6 months ago"
```

If the top contributor doesn't appear in this window, flag it.

**Caveat:** squash-merge compresses authorship — output reflects who merged, not who wrote.

### 3. Bug Clusters

```bash
git log -i -E --grep="fix|bug|broken" --name-only --format='' | sort | uniq -c | sort -nr | head -20
```

Files with the most bug-related commits. Depends on commit message discipline — useless if the team writes "update stuff".

Compare against #1 — files on both lists are your highest risk.

### 4. Project Velocity

```bash
git log --format='%ad' --date=format:'%Y-%m' | sort | uniq -c
```

Commit count by month.

| Pattern | Meaning |
|---------|---------|
| Steady rhythm | Healthy |
| Count drops by half in a month | Someone probably left |
| Declining over 6-12 months | Team losing momentum |
| Periodic spikes followed by quiet | Batch releases, not continuous shipping |

### 5. Firefighting Frequency

```bash
git log --oneline --since="1 year ago" | grep -iE 'revert|hotfix|emergency|rollback'
```

Revert/hotfix/emergency count in the past year. A handful per year is normal. Every couple weeks = team doesn't trust its deploy process.

Zero is also a signal — either stable or nobody writes descriptive messages.

---

## License

MIT