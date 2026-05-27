Made a switch to ssh alternative: https://github.com/trzsz/trzsz-ssh, sshpass got rate limit if called too frequently, by utilizing SSH Multiplexing (also known as a persistent ControlMaster connection) combined with tssh, the mod is able to overcome openssh's frequent connection lockup.

# opencode-ssh

Run opencode on a remote server. Say "ssh myHost" and all commands run remotely.

- No need to install opencode remotely or expose API keys on the server.
- Reuses the same open ssh connection for performance, remembers last working folder, remote open docker connections etc.

Use to rapidly investigate incidents on remote servers, troubleshoot, analyze logs, data etc.

## Setup

**1. Copy the plugin**

```sh
mkdir -p ~/.config/opencode/plugins
cp src/index.ts ~/.config/opencode/plugins/remote.ts
```

**2. Create an agent**

`.opencode/agent/remote.md`:
```markdown
---
description: Run commands on a remote server via SSH
color: primary
mode: primary
---

When the user says "ssh <host>", use `ssh_connect` with the host name.
When they say "local", use `ssh_disconnect`.
```

## Usage

1. Switch to the **Remote** agent tab.
2. Say **"ssh myHost"** (replace `myHost` with a host from `~/.ssh/config`).
3. Ask anything — `bash` commands automatically run on the remote server.
4. Say **"local"** to disconnect.

## How it works

- Opens SSH ControlMaster (`ssh -MNf ...`) for a persistent connection.
- Hooks into `tool.execute.before` to wrap `bash` calls with SSH at runtime.
- Disables local file tools (`read`, `write`, `edit`, `glob`, `grep`) in remote mode.
- Injects remote-mode instructions into the system prompt.

## Example `~/.ssh/config`

```
Host myHost
    Hostname 192.100.100.100
    User username
    ServerAliveInterval 60
```

On the remote server, add your public key in `~/.ssh/authorized_keys`

With this entry, you say "ssh myHost" to connect. The `Host` value is the name you use in conversation.
