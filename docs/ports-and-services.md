# Ports & Services

Reference for what's running on the homelab server, which ports each service uses, and why they're open. IPs are intentionally omitted — see the network diagram in the README for the topology instead.

## Access & management

| Service | Default port | Why it's open |
|---|---|---|
| SSH | 22 | Headless server administration from the Working Area |
| Pi-hole (DNS) | 53 | Serves DNS to devices that opt into using it as their resolver |
| Pi-hole (web admin) | 80 | Dashboard for viewing/managing blocklists and query logs |
| Portainer | 9000 (HTTP) / 9443 (HTTPS) | Web UI for managing Docker containers without the CLI |
| Cockpit | 9090 | Server health/resource monitoring dashboard |
| Tailscale | N/A (outbound mesh, no listening port exposed) | Encrypted remote access without forwarding any ports to the internet |

## Media stack

| Service | Default port | Why it's open |
|---|---|---|
| Jellyfin | 8096 (HTTP) / 8920 (HTTPS) | Media streaming web UI and clients |
| Jellyseerr | 5055 | Request interface for new movies/shows |
| Sonarr | 8989 | TV series automation/management |
| Radarr | 7878 | Movie automation/management |
| Prowlarr | 9696 | Indexer/tracker sync across Sonarr and Radarr |
| Transmission | 9091 (web UI) / 51413 (peer traffic) | BitTorrent client used by the automation stack |

## Access policy

- All ports above are reachable **only from the Working Area** (trusted laptop + phone) on the local network — see [network diagram](network-diagram.png).
- The server does **not** initiate outbound connections back into the Working Area; this is enforced with UFW.
- Nothing here is forwarded to the public internet. Remote access happens exclusively through Tailscale's encrypted mesh.
- No real IP addresses are published in this repo — only private LAN placeholder ranges (e.g. `192.168.0.0/24`) are used where relevant.
