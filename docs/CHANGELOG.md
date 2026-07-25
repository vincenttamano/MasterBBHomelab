# Changelog

All notable changes to this homelab build are documented here, newest first. Format loosely follows [Keep a Changelog](https://keepachangelog.com/).

## [Unreleased]

### Planned
- Apply UFW firewall rules restricting inbound traffic per service
- Migrate media stack to Docker Compose
- Upgrade from UFW-based segmentation to true VLAN isolation once VLAN-capable hardware is available
- Set up automated backups for service configs

## 2026-07-24 to 2026-07-25

### Added
- Tailscale for encrypted remote access, replacing any need for public port forwarding
- Network segmentation design: Working Area (trusted devices) allowed to reach the server on specific ports; server blocked from initiating outbound connections back into the Working Area

### Fixed
- Corrected an initial design mistake: router-level "Guest Network / AP isolation" blocks traffic in both directions, which would have also blocked trusted devices from reaching the server. Switched to a UFW-based, host-level approach instead.

## 2026-07-23

### Added
- Automated media stack: Jellyfin (streaming), Jellyseerr (requests), Sonarr & Radarr (media finding), Prowlarr (tracker sync), Transmission (torrent downloading)

## 2026-07-22

### Added
- Fresh Debian Linux install on a repurposed Acer Extensa 2519 (Celeron N3060, 4GB RAM, 240GB SSD)
- SSH access configured for headless remote administration
- Pi-hole, Docker, Portainer, and Cockpit installed

### Changed
- Removed the GNOME desktop environment post-install to run fully headless and free up RAM for services

### Fixed
- `masterbb` user wasn't in the `sudo` group after install (Debian only auto-adds the first user to `sudo` if no separate root password is set). Fixed by adding the user to the group manually as root: `usermod -aG sudo masterbb`
