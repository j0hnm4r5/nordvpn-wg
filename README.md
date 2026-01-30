# nordvpn-wg

Generate WireGuard configs for NordVPN using their API. No app required.

Based on [this gist by bluewalk](https://gist.github.com/bluewalk/7b3db071c488c82c604baf76a42eaad3).

## Features

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

```bash
# Download
curl -O https://raw.githubusercontent.com/j0hnm4r5/nordvpn-wg/main/nordvpn-wg
chmod +x nordvpn-wg

# Install system-wide (optional)
sudo mv nordvpn-wg /usr/local/bin/
```

### Gum Edition (Optional)

For a prettier TUI experience using [charmbracelet/gum](https://github.com/charmbracelet/gum):

```bash
curl -O https://raw.githubusercontent.com/j0hnm4r5/nordvpn-wg/main/nordvpn-wg-gum
chmod +x nordvpn-wg-gum
sudo mv nordvpn-wg-gum /usr/local/bin/
```

## Usage

### Quick Start

```bash
# Set token (or skip to enter interactively)
export NORDVPN_ACCESS_TOKEN="your-token-here"

# Best recommended server
nordvpn-wg

# Specific country
nordvpn-wg -c us
nordvpn-wg -c switzerland
nordvpn-wg -c "united kingdom"

# Specific server
nordvpn-wg -s us9574

# Interactive mode (gum version)
nordvpn-wg-gum
```

### Options

| Flag                   | Description                                         |
| ---------------------- | --------------------------------------------------- |
| `-c, --country`        | Filter by country code or name                      |
| `-s, --server`         | Use specific server (e.g., `us9574`)                |
| `-i, --interactive`    | Interactive country picker (fzf, bash version only) |
| `-l, --list-countries` | List all available countries                        |
| `-h, --help`           | Show help                                           |

### Save Locations

When prompted, you can save configs to:

1. **Current directory** - `./server.nordvpn.conf`
2. **/etc/wireguard/** - Enables `sudo wg-quick up servername` (just the name!)
3. **Custom path** - Any path you specify

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
- Private keys are fetched directly from NordVPN's API

## License

MIT
