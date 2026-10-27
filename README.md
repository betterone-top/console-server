[English](README.md) | [Tiếng Việt](README.vi.md) | [简体中文](README.zh-CN.md)

# Betterone Console Server

Pure software deployment with no dedicated hardware required. Runs on standard PCs or Raspberry Pi.

Access physical serial ports on standard PCs or Raspberry Pi via SSH, Web Terminal, Telnet, and Raw TCP, with REST APIs provided for managing configurations and sessions. Supports up to 48 consoles; the free version includes consoles 1–2, while commercial licensing capacity and maintenance coverage are subject to your license.

## Quick Start

For the Windows service edition, run `betterone-cs_<version>_Windows_x86_64_setup.exe`; for Linux, install the `.deb` / `.rpm` package corresponding to your architecture (the service starts automatically upon installation). When opening the web interface for the first time, set the administrator password immediately; there is no default password. For installation and upgrade details, see the [User Manual](docs/customer/cn/USER_MANUAL.md).

For the portable edition, extract the archive and run:

```sh
./betterone-cs --init-config
# On Linux, if non-root users cannot bind to port 80, uncomment http_port in config/settings.toml and change it to 8080.
./betterone-cs
```

On Windows, use `betterone-cs.exe`. Portable mode is enabled by default, storing configuration and data in `config/` and `data/` adjacent to the executable. In the generated `settings.toml`, all items are commented-out defaults; only uncommented items take effect. Changes made via the web UI to Web Address, HTTPS, and Audit settings are stored in the database and are not written back to this file. Access `http://<server-ip>:<port>/`, set an administrator password of at least 8 characters (cannot consist entirely of letters or entirely of numbers), and bind your serial ports in the web console.

## Access & Security

| Access Method | Default Port | Default Status |
| --- | --- | --- |
| Web Management / API / Web Terminal | 80 | Enabled; Console WebSocket path `/console/N`, authentication required by default |
| SSH | 6000 + N | Enabled; password or public key authentication required by default |
| Telnet | 7000 + N | Disabled; authentication required by default once enabled |
| Raw TCP | 5000 + N | Disabled; authentication required by default once enabled |

`N` represents the console number (1–48). The default listening address for newly created configurations is `0.0.0.0`. Telnet and Raw TCP do not listen by default; please verify the listening address before enabling them.

Administrators can independently configure whether authentication is required for each of the four protocols on each console. If authentication is disabled, anyone who can reach the protocol's listening address can access that console directly. Telnet and Raw TCP are unencrypted; even if login is required, usernames and passwords are transmitted in plaintext. Use SSH, HTTPS Web Terminal, or a secure tunnel whenever encryption is required. Never expose a device to an untrusted network before an administrator password has been set.

Each protocol can also independently toggle auxiliary prompts (enabled by default for SSH and WebSocket, disabled by default for Telnet and Raw TCP). When disabled, server-side messages such as serial status and write permissions are omitted, while raw serial data and necessary login prompts (username/password) are retained.

## Serial Port Operations & Batch Configuration

On the Physical Serial Ports page, bound serial ports can open their corresponding console; unbound serial ports can be directly opened by administrators or engineers via "Open Serial Port". Directly opening a port does not save settings, and only one direct session is allowed on the same serial port at a time. Once bound to a console, the current serial parameters are saved together. Consoles support batch modification of serial port parameters, protocol toggles, authentication requirements, auxiliary prompts, connection limits, and write modes.

The System page allows exporting the remarks, hardware bindings, serial parameters, protocol toggles, authentication requirements, auxiliary prompts, connection limits, write modes, and listening addresses for all 48 consoles; it does not include users, API Keys, licenses, or system settings. Importing provides two modes:

- **Batch Configuration** (Default): Applies general settings by console number while preserving the target machine's COM/TTY, USB path/identity bindings, listening IPs, and ports.
- **Full Recovery**: Overwrites all port configurations, suitable for restoring the same machine.

Even devices of the same brand and model may have different COM names, USB serial numbers, physical ports, and local IPs, so full backups cannot be directly transferred blindly; verify that target serial ports support the chosen parameters before applying configurations across machines. Importing may terminate active connections.

Release targets include Windows amd64, as well as Linux amd64, arm64, and armv7 (GOARM=7). Choose according to your target operating system and architecture; physical USB serial ports, drivers, and target system installation behavior should be verified on actual hardware.

## Licensing & Configuration

Currently, directly sold commercial licenses are available in three tiers: **8P, 16P, and 48P**; other tiers are not sold directly. The free version allows using consoles 1–2; actual commercial capacity is determined by the issued license.

Licenses are perpetual. The maintenance period only restricts the release dates of eligible software updates, rather than an expiration date for running the software. Maintenance periods are calculated per device; online ACT activation, RNW renewal, offline request, and file import are all handled on the License page. Device identity is determined by hardware/install/generated identifiers, not network MAC addresses.

In the TOML configuration, specify only the fields you wish to override; unconfigured fields use application defaults. Official release builds hardcode the license server and official portal entry points (license server: `https://lic.cs.betterone.top`), ignoring config overrides and empty values. Only `-tags tst` test builds allow overriding `license_server`, `license_portal_url`, and `license_offline_url` (empty values can disable the corresponding feature or portal). The official project website is fixed to `https://cs.betterone.top`. Upgrades on installed service editions will restart the service and disconnect active sessions; uninstallation retains configuration and data, while factory reset requires local machine confirmation.

## Project & Contact

Official Website: [cs.betterone.top](https://cs.betterone.top)

GitHub: [better-one-top/serial-console-server](https://github.com/better-one-top/serial-console-server)

Email: [betterone-top@hotmail.com](mailto:betterone-top@hotmail.com)
