# nordvpn-wg

Generate WireGuard configs for NordVPN using their API. No app required.

Based on [this gist by bluewalk](https://gist.github.com/bluewalk/7b3db071c488c82c604baf76a42eaad3).

## Features

- 🖥️ Beautiful TUI with interactive prompts
- 🔐 Interactive token input (hidden) or environment variable
- 🌍 Country selection by code (`us`), name (`switzerland`), or partial match (`switz`)
- 🎯 Specific server selection (`us9574`)
- 📁 Save to current directory, `/etc/wireguard/`, or custom path
- 🔒 Proper file permissions (600)

## Prerequisites

- `curl`
- `jq`
- NordVPN subscription with [access token](https://my.nordaccount.com/dashboard/nordvpn/access-tokens/)

## Installation

### TUI Version (Recommended)

The TUI version uses [charmbracelet/gum](https://github.com/charmbracelet/gum) for a beautiful interactive experience.

> [!NOTE]
> Requires `gum`. If not installed, the script will offer to install it via `go install` (requires Go), or show manual installation instructions.

```bash
curl -O https://raw.githubusercontent.com/j0hnm4r5/nordvpn-wg/main/nordvpn-wg-gum
chmod +x nordvpn-wg-gum
sudo mv nordvpn-wg-gum /usr/local/bin/
```

### Plain Bash Version (No Dependencies)

If you prefer no extra dependencies beyond `curl` and `jq`:

```bash
curl -O https://raw.githubusercontent.com/j0hnm4r5/nordvpn-wg/main/nordvpn-wg
chmod +x nordvpn-wg
sudo mv nordvpn-wg /usr/local/bin/
```

> [!TIP]
> Install [fzf](https://github.com/junegunn/fzf) for interactive country selection with `-i` flag.

## Usage

### TUI Version

```bash
# Interactive mode (prompts for everything)
nordvpn-wg-gum

# With country filter
nordvpn-wg-gum -c switzerland

# Specific server
nordvpn-wg-gum -s us9574
```

### Plain Bash Version

```bash
# Best recommended server
nordvpn-wg

# Country by code or name
nordvpn-wg -c us
nordvpn-wg -c switzerland
nordvpn-wg -c "united kingdom"

# Interactive country picker (requires fzf)
nordvpn-wg -i

# Specific server
nordvpn-wg -s us9574

# List all countries
nordvpn-wg -l
```

### Options

| Flag                   | TUI        | Plain | Description                               |
| ---------------------- | ---------- | ----- | ----------------------------------------- |
| `-c, --country`        | ✅         | ✅    | Filter by country code or name            |
| `-s, --server`         | ✅         | ✅    | Use specific server (e.g., `us9574`)      |
| `-o, --output`         | ✅         | ✅    | Output file path (skips save prompt)      |
| `-i, --interactive`    | (built-in) | ✅    | Interactive country picker (requires fzf) |
| `-l, --list-countries` | (built-in) | ✅    | List all available countries              |
| `-h, --help`           | ✅         | ✅    | Show help                                 |

### Scripting (Fully Non-Interactive)

Both versions support fully non-interactive use for automation:

```bash
# Set token via environment variable
export NORDVPN_ACCESS_TOKEN="your-token"

# Generate config without any prompts
nordvpn-wg -c us -o /etc/wireguard/us.conf
nordvpn-wg-gum -c switzerland -o ./swiss.conf
nordvpn-wg -s us9574 -o ~/vpn/specific-server.conf
```

### Save Locations

When prompted, you can save configs to:

1. **Current directory** — `./server.nordvpn.conf`
2. **/etc/wireguard/** — Enables `sudo wg-quick up servername` (just the name!)
3. **Custom path** — Any path you specify

## Connecting

```bash
# If saved to /etc/wireguard/
sudo wg-quick up ch369.nordvpn

# If saved elsewhere
sudo wg-quick up ./ch369.nordvpn.conf

# Disconnect
sudo wg-quick down ch369.nordvpn
```

## Docker / Homelab

These configs work great with containers that support WireGuard, like:

- [hotio/qbittorrent](https://hotio.dev/containers/qbittorrent/)
- [linuxserver/wireguard](https://docs.linuxserver.io/images/docker-wireguard)

Just mount the generated config file into your container.

## Getting Your Access Token

1. Log in to [NordVPN Dashboard](https://my.nordaccount.com/dashboard/nordvpn/)
2. Go to **Access Tokens**
3. Generate a new token
4. Set it: `export NORDVPN_ACCESS_TOKEN="your-token"`

Or just run the script and paste it when prompted (input is hidden).

## Security

- Tokens are never passed as command-line arguments (visible in `ps`)
- Interactive input uses `read -s` (hidden)
- Generated configs have `chmod 600` permissions
- User input is sanitized before use in URLs and queries
- Private keys are fetched directly from NordVPN's API

## License

MIT
