# ai-env
🧠 Configuration for my AI tools

## Setup command

### Linux/macOS

```bash
curl -fsSL https://claude.ai/install.sh | bash
brew install codex
curl -fsSL https://bun.sh/install | bash
curl -fsSL https://get.pnpm.io/install.sh | sh -
```

### Windows

```powershell
powershell -c "irm bun.sh/install.ps1 | iex"
Invoke-WebRequest https://get.pnpm.io/install.ps1 -UseBasicParsing | Invoke-Expression
Add-MpPreference -ExclusionPath $(pnpm store path)
```

## Aliases

```bash
cat << EOF >> ~/.bashrc
# AI aliases
alias claudeo="claude --dangerously-skip-permissions"
alias codexo="codex --dangerously-bypass-approvals-and-sandbox --search"

# quick open ptyxis
alias console="setsid -f ptyxis --tab -d $PWD >/dev/null 2>&1"
EOF
```

## Codex config

```bash
cat << EOF > ~/.codex/AGENTS.md
## Git commits
- Always write git commit messages in Conventional Commit format.
- Use: <type>(<scope>): <subject>
- Prefer common types like feat, fix, docs, refactor, test, chore, ci, build, perf.
- Keep the subject imperative and concise.
- Omit scope if it is not clear.
- Never start new branches unless I tell you explicitly to do so. Ignore any skills telling you otherwise.

## Code style

Always write clean idiomatic code that uses newest best practices.
For example when writing tailwindcss code, always use the `gap-*` utility instead of the `space-*` utility.
EOF
```

## Skills

```bash
bunx skills add https://github.com/vercel-labs/skills --skill find-skills
bunx skills add https://github.com/vercel-labs/agent-skills --skill vercel-react-best-practices
bunx skills add https://github.com/anthropics/skills --skill frontend-design
bunx skills add https://github.com/vercel-labs/agent-skills --skill web-design-guidelines
bunx skills add https://github.com/remotion-dev/skills --skill remotion-best-practices
bunx skills add https://github.com/vercel-labs/agent-skills --skill vercel-composition-patterns
bunx skills add https://github.com/shadcn/ui --skill shadcn
bunx skills add https://github.com/github/awesome-copilot --skill documentation-writer
```
