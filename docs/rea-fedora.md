# REA setup on Fedora

Use this guide after the main [setup command](../README.md#setup-command).
It configures REA 6.7.0 for Codex, Claude Code, and OpenCode on Fedora 44.
The examples use `/home/alex`. Change this path for another account.

## Prepare the tools

The main install command includes `android-tools`, `wireshark-cli`, `sigrok-cli`, `binwalk`, `python3-pwntools`, and `nodejs24`.
These provide ADB, TShark and capinfos, sigrok, Binwalk, pwntools, and Node.js.

REA's pwntools provider requires pwntools 4.15.0, pyelftools 0.33, and Unicorn 2.1.2.
Fedora's pwntools package supplies the first two, but Unicorn needs a separate installation.
Create an isolated Python environment that can use the Fedora packages:

```bash
/usr/bin/python3 -m venv --system-site-packages "$HOME/.local/share/rea/pwntools-venv"
"$HOME/.local/share/rea/pwntools-venv/bin/python" -m pip install unicorn==2.1.2
```

Ghidra is installed separately at `/home/alex/.local/opt/ghidra_12.1.4_PUBLIC` on this workstation.
Set `GHIDRA_INSTALL_DIR` to the actual installation directory on another machine.
Ghidra 12.1.4 also needs a 64-bit JDK 21 or later.

SQLite inspection requires Node.js 24.15 or later in the 24.x series, or Node.js 26 or later.
Installing `nodejs24` does not switch a launcher that still resolves a pnpm-managed Node.js runtime.
The examples launch `/usr/bin/npx` and put `/usr/bin` first in the server's `PATH`.
This selects the system Node.js runtime for REA and its workers.
Append other stable tool directories to `PATH` if needed.

## Configure the clients

Back up each configuration file before editing it.
Merge the REA entry into the existing file and preserve other servers and settings.
Set the variables in the MCP entry so that desktop apps receive them too.

Each example sets Ghidra's import and analysis deadline to 60 minutes (`3600000` milliseconds).
REA's default is 330 seconds, which is too short for a large executable's first analysis.
The client tool timeout is 65 minutes, which leaves time for the requested operation after startup.

`GHIDRA_HEADLESS_MAXMEM` sets the Java heap for headless Ghidra. REA also reads `GHIDRA_MAXMEM`.
REA's default is `2G`. With that heap, a 324 MB Windows executable did not finish its first analysis in 30 minutes.
The examples use `16G` for a workstation with 62 GB of RAM. Use a smaller value on a machine with less memory.

### Codex

Update the existing `[mcp_servers.rea]` entry in `~/.codex/config.toml`:

```toml
[mcp_servers.rea]
command = "/usr/bin/npx"
args = ["-y", "rea-agents@6.7.0", "mcp"]
startup_timeout_sec = 30
tool_timeout_sec = 3900

[mcp_servers.rea.env]
GHIDRA_INSTALL_DIR = "/home/alex/.local/opt/ghidra_12.1.4_PUBLIC"
REA_GHIDRA_STARTUP_TIMEOUT_MS = "3600000"
GHIDRA_HEADLESS_MAXMEM = "16G"
REA_TSHARK_COMMAND = "/usr/bin/tshark"
REA_CAPINFOS_COMMAND = "/usr/bin/capinfos"
REA_SIGROK_COMMAND = "/usr/bin/sigrok-cli"
REA_BINWALK_COMMAND = "/usr/bin/binwalk"
REA_PWNTOOLS_PYTHON = "/home/alex/.local/share/rea/pwntools-venv/bin/python"
PATH = "/usr/bin:/bin:/usr/local/bin"
```

`startup_timeout_sec` covers the MCP connection. Ghidra starts when an analysis operation needs it, so its deadline belongs in `REA_GHIDRA_STARTUP_TIMEOUT_MS`.

### Claude Code

Update `mcpServers.rea` in `~/.claude.json`:

```json
{
  "mcpServers": {
    "rea": {
      "command": "/usr/bin/npx",
      "args": ["-y", "rea-agents@6.7.0", "mcp"],
      "timeout": 3900000,
      "env": {
        "GHIDRA_INSTALL_DIR": "/home/alex/.local/opt/ghidra_12.1.4_PUBLIC",
        "REA_GHIDRA_STARTUP_TIMEOUT_MS": "3600000",
        "GHIDRA_HEADLESS_MAXMEM": "16G",
        "REA_TSHARK_COMMAND": "/usr/bin/tshark",
        "REA_CAPINFOS_COMMAND": "/usr/bin/capinfos",
        "REA_SIGROK_COMMAND": "/usr/bin/sigrok-cli",
        "REA_BINWALK_COMMAND": "/usr/bin/binwalk",
        "REA_PWNTOOLS_PYTHON": "/home/alex/.local/share/rea/pwntools-venv/bin/python",
        "PATH": "/usr/bin:/bin:/usr/local/bin"
      }
    }
  }
}
```

Claude Code's per-server `timeout` uses milliseconds and overrides `MCP_TOOL_TIMEOUT` for REA.
It belongs beside `command` and `args`, outside `env`.
See [Claude Code's tool timeout documentation](https://code.claude.com/docs/en/mcp#time-limits-on-mcp-tool-calls).

### OpenCode v2

Update `mcp.servers.rea` in `~/.config/opencode/opencode.jsonc`:

```json
{
  "mcp": {
    "servers": {
      "rea": {
        "type": "local",
        "command": ["/usr/bin/npx", "-y", "rea-agents@6.7.0", "mcp"],
        "disabled": false,
        "timeout": { "execution": 3900000 },
        "environment": {
          "GHIDRA_INSTALL_DIR": "/home/alex/.local/opt/ghidra_12.1.4_PUBLIC",
          "REA_GHIDRA_STARTUP_TIMEOUT_MS": "3600000",
          "GHIDRA_HEADLESS_MAXMEM": "16G",
          "REA_TSHARK_COMMAND": "/usr/bin/tshark",
          "REA_CAPINFOS_COMMAND": "/usr/bin/capinfos",
          "REA_SIGROK_COMMAND": "/usr/bin/sigrok-cli",
          "REA_BINWALK_COMMAND": "/usr/bin/binwalk",
          "REA_PWNTOOLS_PYTHON": "/home/alex/.local/share/rea/pwntools-venv/bin/python",
          "PATH": "/usr/bin:/bin:/usr/local/bin"
        }
      }
    }
  }
}
```

OpenCode v2 uses `mcp.servers`, `disabled`, and a timeout object.
For an older configuration, move all server entries under `mcp.servers` and replace `enabled` with its opposite `disabled` value.
This preserves other servers when migrating the configuration.
See [OpenCode's MCP documentation](https://opencode.ai/docs/mcp-servers/#timeouts).

## Restart and verify

Reconnect REA or restart the affected agent process to load the configuration.
Make sure that the CLI process reloads its MCP configuration before retrying the analysis.

Call `binary_session` and check the Ghidra provider and tool availability.
Use `inspect_adb_client`, `inspect_signal_decoders`, and `inspect_packet_fields` to check the installed tools without contacting devices or capturing traffic.
Use disposable local fixtures to check `inspect_binary_layout`, `inspect_firmware_regions`, and `inspect_sqlite_database`.

A tool marked `available` still needs a successful operation to establish that its dependencies work.
A `target_required` result means that the operation needs an opened artifact.

## Large binaries and remaining prerequisites

The longer timeout and larger heap allow more time and memory for the first Ghidra analysis. They do not reuse an unrelated cached analysis.
After a successful first analysis, pass `snapshot_path` to `close_binary` to keep the result for later sessions.
`REA_GHIDRA_DISABLED_ANALYZERS` can skip expensive analyzers, but this changes analysis coverage.
Do not claim that a large binary succeeds until its analysis finishes.

REA 6.7.0 specifies WABT 1.0.42. Fedora 44 currently provides 1.0.40, so install the required version separately for WebAssembly inspection.
Android decompilation needs the separate `jadx-headless-mcp` 0.7.1 JAR.
Apktool and Wakaru also need separate installation.
Apple-specific inspection still requires macOS, and commercial engines need their own licenses.
