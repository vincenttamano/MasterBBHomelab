# MasterBBHomelab

A self-hosted home server built from a repurposed Acer laptop — running headless Debian Linux with a Docker-based service stack for media, network management, and remote access.

## Why

Wanted hands-on experience with real server administration — installing Linux from scratch, managing everything over SSH, containerizing services, and thinking through network security — instead of just reading about it. This repo documents that process end to end, including the mistakes.

## Hardware

| Component | Spec |
|---|---|
| Device | Acer Extensa 2519 (repurposed laptop) |
| CPU | Intel Celeron N3060 @ 1.6GHz |
| RAM | 4GB |
| Storage | 240GB Kingmax SSD |
| OS | Debian 13 (headless, no desktop environment) |
|Physical Setup Pictures |[Setup](docs/setup/server.jpg)[2](docs/setup/server-and-mainLaptop.jpg) |

## Architecture

The server runs headless, managed entirely over SSH from a separate main laptop. Services are containerized with Docker and managed via Portainer.

![Network diagram](docs/network-diagram.png)

- **Working Area**: main laptop + phone, on the same flat LAN as the server
- **Lab Area**: the homelab server, reachable from the Working Area on specific allowed ports only (SSH, Pi-hole, Jellyfin, Portainer, Cockpit) — outbound traffic from the server toward personal devices is blocked via UFW
- **Remote access**: Tailscale only — no ports forwarded directly to the internet

## Services

**Network & management**
- **Pi-hole** — network-wide DNS ad/tracker blocking
- **Tailscale** — encrypted remote access without exposing ports publicly, installed on both the server and phone for on-the-go access to the homelab
- **Portainer** — web UI for managing Docker containers
- **Cockpit** — server health/resource monitoring
- **UFW** — host-level firewall, restricting inbound traffic to only what each service needs

**Media stack**
- **Jellyfin** — media streaming server
- **Jellyseerr** — request interface for new movies/shows
- **Sonarr** / **Radarr** — automated finding of requested series/movies
- **Prowlarr** — syncs indexers/trackers across Sonarr and Radarr
- **Transmission** — BitTorrent client used by the automation stack

## Build log

| Date | Milestone |
|---|---|
| Jul 22, 2026 | Installed Debian Linux (replacing the original OS), configured SSH access, removed the GNOME desktop environment to run fully headless, and installed Pi-hole, Docker, Portainer, and Cockpit |
| Jul 23, 2026 | Built an automated media system using **Jellyfin** (streaming), **Jellyseerr** (requests), **Sonarr** & **Radarr** (media finding), **Prowlarr** (tracker sync), and **Transmission** (torrent downloader) |
| Jul 24–25, 2026 | Configured **Tailscale** for secure remote access, and designed network segmentation using **UFW** to allow only specific inbound ports from personal devices to the server while blocking outbound traffic from the server toward them |
| Jul 25, 2026 | Applied and verified UFW firewall rules (default deny-incoming, LAN-only access per service, Tailscale interface allowed); installed Tailscale on phone for remote access on the go |

## Lessons learned

- Debian's netinst doesn't add the first user to the `sudo` group if you set a separate root password during install — worth knowing before you're locked out of `sudo`.
- DNS-level ad blocking (Pi-hole) can't stop everything — on-page popups/redirects need a browser-level blocker (uBlock Origin) alongside it.
- Browsers and OSes can have their own DNS settings (e.g. Chrome/Opera's "secure DNS") that silently override your network DNS config — worth checking if Pi-hole seems to not be working.
- Router-level "Guest Network / AP isolation" blocks traffic in both directions, not just one — putting the server there would've also blocked trusted devices from reaching it. Real one-way access (trusted → server allowed, server → trusted blocked) needs a host firewall (UFW) or VLAN rules, not a simple isolation toggle.

## Roadmap

- [x] Apply UFW firewall rules restricting inbound traffic per service
- [ ] Migrate media stack to Docker Compose
- [ ] Upgrade from software-level segmentation to true VLAN isolation once VLAN-capable hardware is available
- [ ] Set up automated backups for service configs

## Screenshots

| Screenshot | Shows |
|---|---|
| [Pi-hole dashboard](docs/screenshots/pihole-dashboard.png) | Query stats, blocked domains, block percentage |
| [Portainer containers](docs/screenshots/portainer-containers.png) | Full running service stack |
| [Cockpit overview](docs/screenshots/cockpit-overview.png) | System health and resource usage |
| [Jellyfin library](docs/screenshots/jellyfin-library.png) | Media streaming interface |
| [Sonarr/Radarr activity](docs/screenshots/sonarr-radarr-activity.png) | Automated media pipeline in action |
| [SSH session](docs/screenshots/ssh-session.png) | Headless remote server administration |
| [UFW firewall status](docs/screenshots/ufw-status.png) | Verified firewall rules enforcing segmentation |
| [Tailscale Demo](docs/screenshots/tailscale-phone.jpg) | Remote Access on Phone on Different Network |

*(Add each image to `docs/screenshots/` with the filename above, or update the links here to match whatever you name them.)*

## Future plans: SOC homelab

Longer-term goal is to extend this from a media/self-hosting box into a small Security Operations Center (SOC) homelab — giving hands-on practice with the tools and workflows used in real blue-team/SOC roles.

**Planned additions**
- **Wazuh** — SIEM/XDR platform for centralized log collection, alerting, and endpoint monitoring across the homelab
- **Suricata** — network intrusion detection (IDS), watching traffic for known attack signatures and anomalies
- **Cowrie** — SSH/Telnet honeypot to capture and study real-world attack attempts against the network
- **OpenVAS / Greenbone** — vulnerability scanning to regularly check the homelab's own services for exposures
- **ELK Stack (Elasticsearch, Logstash, Kibana)** or Wazuh's built-in stack — log aggregation and dashboarding for the whole environment
- **pfSense or OPNsense** — replacing the ISP router with a proper firewall, enabling real VLAN segmentation (ties into the network segmentation upgrade already in the Roadmap) and firewall-level logging

**Why this direction**
This builds directly on the segmentation and firewall work already documented above — instead of just blocking traffic, the SOC phase is about *observing, detecting, and investigating* it. Practical goal: be able to simulate a basic attack against the homelab (e.g. a port scan or brute-force attempt) and walk through detecting it end-to-end in Wazuh/Suricata, the same way a junior SOC analyst would triage an alert.

## Specs & credits

Built and documented by [Vincent John Tamaño](https://github.com/vincenttamano).
