# ai-env
🧠 Configuration for my AI tools

## Setup command

### Linux/macOS

```bash
curl -fsSL https://claude.ai/install.sh | bash
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
curl -fsSL https://chatgpt.com/codex/install.sh | sh
curl -fsSL https://bun.sh/install | bash
bun add --global agent-browser
agent-browser install
curl -fsSL https://get.pnpm.io/install.sh | sh -
curl -fsSL https://opencode.ai/install | bash
curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs | sh
curl -sL https://git.io/go-installer | bash
sudo dnf install protobuf-compiler gcc cmake autoconf make git curl wget python ruby perl sqlite3 prometheus lm_sensors putty wireguard-tools postgresql

sudo dnf install sway grim wf-recorder libxkbcommon-devel

# Update Rust
rustup update stable

# Configure Git identity and SSH commit signing
git config --global user.name "AlexProgrammerDE"
git config --global user.email "40795980+AlexProgrammerDE@users.noreply.github.com"
git config --global gpg.format ssh
git config --global user.signingkey "$HOME/.ssh/id_rsa.pub"
git config --global commit.gpgsign true
git config --global tag.gpgSign true

# Prevent high ram usage from freezing the whole system
sudo dnf install earlyoom
sudo systemctl enable --now earlyoom

# Add good file archive browser
sudo dnf install file-roller unzip p7zip p7zip-plugins unrar

# C dev dependencies
sudo dnf install valgrind libasan libubsan

# Raise ulimits
sudo tee /etc/security/limits.d/99-local-workstation.conf >/dev/null <<'EOF'
# General desktop/development/gaming defaults.
# Applies to PAM login sessions after logging out and back in.

* soft nofile 65535
* hard nofile 1048576

* soft nproc 65535
* hard nproc 262144

* soft stack 8192
* hard stack unlimited
EOF

# RDP clients
sudo dnf install gnome-connections remmina

# Proton tools
curl -fsSL https://proton.me/download/pass-cli/install.sh | bash
URL="https://proton.me/download/drive/$(curl -fsSL https://proton.me/download/drive/cli/index.html | grep -oE 'cli/[0-9]+\.[0-9]+\.[0-9]+/linux-x64/proton-drive' | sort -uV | tail -1)"
curl -fL -o /tmp/proton-drive "$URL" && sudo install -m 0755 /tmp/proton-drive /usr/local/bin/proton-drive && proton-drive version
sudo dnf install "https://repo.protonvpn.com/fedora-$(rpm -E %fedora)-stable/protonvpn-stable-release/protonvpn-stable-release-1.0.5-1.noarch.rpm"
sudo dnf check-update --refresh; sudo dnf install proton-vpn-gnome-desktop
sudo dnf install "$(curl -fsSL https://proton.me/download/current_version_linux.json | python3 -c 'import json,sys; print(json.load(sys.stdin)["RpmFile"])')"

# Go CLIs
go install github.com/evilmartians/lefthook/v2@v2.2.2

# Rust CLIs
cargo +stable install openrouter-cli --locked
```

#### Linux: Log into proton tools

We got proton tools now, lets log in:

```bash
pass-cli login
proton-drive auth login
protonvpn signin <your-username>
protonmail-bridge --cli   # then type: login
```

#### Linux: increase the inotify instance limit

Desktop tools and game clients can exhaust the default limit. Set the limit to 512 and apply it now:

```bash
sudo tee /etc/sysctl.d/99-local-inotify.conf >/dev/null <<'EOF'
fs.inotify.max_user_instances = 512
EOF
sudo sysctl -w fs.inotify.max_user_instances=512
```

#### Increase swap

```bash
sudo mkdir -p /etc/systemd/zram-generator.conf.d
sudo tee /etc/systemd/zram-generator.conf.d/99-size.conf >/dev/null <<'EOF'
[zram0]
zram-size = 32768
EOF
```

#### Keep temporary files off the `/tmp` ramdisk

On Fedora, `/tmp` is a RAM-backed tmpfs with a per-user quota. AI tools and the dev tools they run can fill it, and then every write to `/tmp` fails with `EDQUOT`.
Set `TMPDIR` to a disk-backed directory for the whole login session, and delete old files there automatically:

```bash
mkdir -p ~/.config/environment.d ~/.config/user-tmpfiles.d

tee ~/.config/environment.d/50-tmpdir.conf >/dev/null <<'EOF'
TMPDIR=${HOME}/.cache/tmp
CLAUDE_CODE_TMPDIR=${HOME}/.cache/claude-tmp
EOF

# Delete files older than the given age. The systemd-tmpfiles-clean user timer runs this daily.
tee ~/.config/user-tmpfiles.d/agent-tmp.conf >/dev/null <<'EOF'
d %h/.cache/tmp 0700 - - 3d
d %h/.cache/claude-tmp 0700 - - 7d
EOF

systemd-tmpfiles --user --create
```

Log out and back in to apply `TMPDIR`. `environment.d` applies to GUI apps and terminals, so it also covers agents started from desktop apps.

#### Fedora QoL

```bash
gsettings set org.gnome.desktop.wm.preferences focus-new-windows 'strict'
gsettings set org.gnome.desktop.wm.preferences resize-with-right-button true
gsettings set org.gnome.settings-daemon.plugins.power sleep-inactive-ac-type 'nothing'
gsettings set org.gnome.desktop.screensaver restart-enabled true

flatpak install flathub \
  it.mijorus.gearlever \
  com.github.tchx84.Flatseal \
  io.github.flattool.Warehouse \
  io.missioncenter.MissionCenter \
  org.localsend.localsend_app
```

### Windows

```powershell
powershell -c "irm bun.sh/install.ps1 | iex"
bun add --global agent-browser
agent-browser install
Invoke-WebRequest https://get.pnpm.io/install.ps1 -UseBasicParsing | Invoke-Expression
Add-MpPreference -ExclusionPath $(pnpm store path)
```

## Aliases

```bash
cat << EOF >> ~/.bashrc
# AI aliases
alias claudeo="claude --dangerously-skip-permissions"
alias codexo="codex --dangerously-bypass-approvals-and-sandbox --search"
alias opencodeo="OPENCODE_CONFIG_CONTENT='{\"permission\":\"allow\"}' opencode"
alias t3="pnpm dlx t3"

# quick open ptyxis
alias console="setsid -f ptyxis --tab -d $PWD >/dev/null 2>&1"
EOF
```

## AGENTS.md global config

```bash
wget -O ~/.codex/AGENTS.md https://raw.githubusercontent.com/AlexProgrammerDE/ai-env/refs/heads/main/GLOBAL_AGENTS.md
wget -O ~/.claude/CLAUDE.md https://raw.githubusercontent.com/AlexProgrammerDE/ai-env/refs/heads/main/GLOBAL_AGENTS.md
wget -O ~/.config/opencode/AGENTS.md https://raw.githubusercontent.com/AlexProgrammerDE/ai-env/refs/heads/main/GLOBAL_AGENTS.md
```

Keep `GLOBAL_AGENTS.md` limited to personal working agreements: Git, implementation,
verification, writing, and temporary files. Put project commands, architecture, and framework rules
in each repository's `AGENTS.md`. Put detailed procedures in skills and load them
only for relevant tasks.

## Setup Codex/ChatGPT

You really should not use the default writing style. ChatGPT/Codex is a massive yapper and we want an efficient tool, not a buddy.
So set this writing style:

<img width="394" height="343" alt="ChatGPT efficient writing style" src="https://github.com/user-attachments/assets/f7338d30-a564-42c4-bdb0-0329ec8f4c60" />

## `AGENTS.md`/`CLAUDE.md`

You should always write a repo-level `AGENTS.md` and then write a `CLAUDE.md` with the content:
```text
@AGENTS.md
```

Claudes preprocessor will import the AGENTS.md contents automatically.

## Skills

The PR skills use an authenticated GitHub CLI (`gh`).
`babysit-pr` also needs Python 3.
`before-and-after` needs `agent-browser` and GitHub CLI 2.99 or later.

```bash
bunx skills add https://github.com/vercel-labs/skills --skill find-skills --global --yes --agent claude-code opencode codex
bunx skills add https://github.com/vercel-labs/agent-skills --skill vercel-react-best-practices --global --yes --agent claude-code opencode codex
bunx skills add https://github.com/anthropics/skills --skill frontend-design --global --yes --agent claude-code opencode codex
bunx skills add https://github.com/vercel-labs/agent-skills --skill web-design-guidelines --global --yes --agent claude-code opencode codex
bunx skills add https://github.com/remotion-dev/skills --skill remotion-best-practices --global --yes --agent claude-code opencode codex
bunx skills add https://github.com/vercel-labs/agent-skills --skill vercel-composition-patterns --global --yes --agent claude-code opencode codex
bunx skills add https://github.com/shadcn/ui --skill shadcn --global --yes --agent claude-code opencode codex
bunx skills add https://github.com/github/awesome-copilot --skill documentation-writer --global --yes --agent claude-code opencode codex
bunx skills add aminblg/simpleenglish@simple-english --global --yes --agent claude-code opencode codex
bunx skills add blader/humanizer --skill humanizer --global --yes --agent claude-code opencode codex
bunx skills add https://github.com/vercel-labs/before-and-after --skill before-and-after --global --yes --agent claude-code opencode codex
bunx skills add https://github.com/t3dotgg/tstack --skill file-pr babysit-pr leaving-pr-comments --global --yes --agent claude-code opencode codex

# Run locally, very invasive:
# bunx skills add https://github.com/effect-ts/skills --skill effect-ts --yes --agent claude-code opencode codex
```

### Resync skills

Run this on each machine to reinstall the skills listed above from the latest README.
It preserves other installed skills.
Bun must be installed.

```bash
(
  set -euo pipefail

  skills_tmp=$(mktemp -d)
  trap 'rm -rf "$skills_tmp"' EXIT

  curl -fsSL \
    https://raw.githubusercontent.com/AlexProgrammerDE/ai-env/main/README.md \
    -o "$skills_tmp/README.md"

  awk '/^## Skills$/{skills=1; next}
       skills && /^## /{exit}
       skills && /^bunx skills add /{print}' \
    "$skills_tmp/README.md" > "$skills_tmp/install.sh"

  test -s "$skills_tmp/install.sh"

  bash -e "$skills_tmp/install.sh" </dev/null
)
```

The commands run from a file with stdin disconnected.
This prevents an installer from consuming later commands as input.

## MCP

```bash
codex mcp add playwright bunx "@playwright/mcp@latest"
claude mcp add playwright bunx @playwright/mcp@latest

codex mcp add shadcn bunx "shadcn@latest" "mcp"
claude mcp add shadcn bunx shadcn@latest mcp

npx -y @posthog/wizard@latest mcp add

npx -y rea-agents setup --yes --all-detected
```
