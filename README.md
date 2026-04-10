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

## AGENTS.md global config

```bash
wget -O ~/.codex/AGENTS.md https://raw.githubusercontent.com/AlexProgrammerDE/ai-env/refs/heads/main/GLOBAL_AGENTS.md
wget -O ~/.claude/CLAUDE.md https://raw.githubusercontent.com/AlexProgrammerDE/ai-env/refs/heads/main/GLOBAL_AGENTS.md
```

## Skills

```bash
bunx skills add https://github.com/vercel-labs/skills --skill find-skills --global --yes
bunx skills add https://github.com/vercel-labs/agent-skills --skill vercel-react-best-practices --global --yes
bunx skills add https://github.com/anthropics/skills --skill frontend-design --global --yes
bunx skills add https://github.com/vercel-labs/agent-skills --skill web-design-guidelines --global --yes
bunx skills add https://github.com/remotion-dev/skills --skill remotion-best-practices --global --yes
bunx skills add https://github.com/vercel-labs/agent-skills --skill vercel-composition-patterns --global --yes
bunx skills add https://github.com/shadcn/ui --skill shadcn --global --yes
bunx skills add https://github.com/github/awesome-copilot --skill documentation-writer --global --yes
```
