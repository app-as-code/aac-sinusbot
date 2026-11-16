<h1 align="center">aac-sinusbot</h1>

<p align="center">Self-hosted deployment definition for SinusBot, a music bot for TeamSpeak 3 and Discord.</p>

<p align="center">
  <a href="https://www.sinusbot.com"><img alt="Upstream" src="https://img.shields.io/badge/upstream-sinusbot.com-blue"></a>
  <a href="https://hub.docker.com/r/sinusbot/docker"><img alt="Image" src="https://img.shields.io/badge/image-sinusbot%2Fdocker%3A1.0.2--discord-2496ED?logo=docker&logoColor=white"></a>
  <img alt="License" src="https://img.shields.io/badge/license-proprietary%20(personal%20use)-red">
</p>

## About

SinusBot is a bot that plays music and other audio in TeamSpeak 3 and Discord channels. It is
controlled through a web interface and supports custom scripts, so its behaviour can be
extended. The image bundles an integrated text-to-speech engine.

Key features (as provided by upstream):

- Plays audio into TeamSpeak 3 and Discord channels
- Web interface for control and configuration
- Custom script support through a mountable scripts directory
- Integrated text-to-speech engine
- Runs on Linux and macOS through Docker

## Upstream

- Project: <https://www.sinusbot.com>
- Container image: `sinusbot/docker:1.0.2-discord` (Docker Hub, <https://hub.docker.com/r/sinusbot/docker>)
- License: proprietary. Free for personal use only; commercial use or redistribution requires
  written permission from the author (see Credits and license below).

## Quick start (Docker Compose)

The Compose file is rendered from `service.yml` by the App-as-Code template engine. For a
manual run, create the host directories for the two volumes and start the service:

```bash
mkdir -p /export/docker/aac-sinusbot/scripts /export/docker/aac-sinusbot/data

# Provide the values listed under "Configuration" in .env (OVERRIDE_PASSWORD must be set)
docker compose up -d
```

Secrets are not stored in this repository. Provide them through the deployment pipeline or
create the `.env` file yourself (see below). Do not commit them.

## Configuration

Environment variables (defaults from `service.yml`; `CHANGE_ME` must be set per deployment):

| Variable | Default | Description |
|---|---|---|
| `OVERRIDE_PASSWORD` | `CHANGE_ME` (secret) | Password override for the SinusBot web interface |
| `SINUSBOT_PORT` | `8087` | Port the SinusBot web interface listens on inside the container |
| `SINUSBOT_MEMORY_LIMIT` | `512M` | Memory limit passed to SinusBot |
| `PUID` / `PGID` | `1000` | User and group ID the container runs as |
| `UID` / `GID` | `1000` | User and group ID passed to the container |
| `TZ` | `Europe/Berlin` | Time zone |

Ports:

| Port | Protocol | Purpose |
|---|---|---|
| `8087` (external `28087`) | TCP | Web interface (`web`) |

Volumes:

| Mount | Description |
|---|---|
| `scripts` -> `/opt/sinusbot/scripts` | Custom SinusBot scripts |
| `data` -> `/opt/sinusbot/data` | SinusBot main data and configuration |

Runtime settings from `service.yml`: restart policy `always`, `json-file` logging (10 MB per
file, 10 files), `privileged: true`, added capabilities `SYS_NICE` and `NET_ADMIN`, and
`seccomp:unconfined`. The privileged mode and extra capabilities are required by this
deployment definition; review them before running it in a shared environment.

## Deploy with App-as-Code

1. Edit `service.yml`. It is the single source of truth for image, ports, environment,
   volumes and per-stage overrides. Do not edit the rendered output.
2. Pushing to `main` (or a configured stage branch) runs the service pipeline, which renders
   the manifests with `aac-template-engine` and triggers the deployment.
3. Releases of this definition are published under <https://github.com/app-as-code/aac-sinusbot>.

## Credits and license

SinusBot is developed by Michael Friese (© 2013 onwards) and is distributed under the
upstream SinusBot license. According to the upstream Docker Hub description, the software is
free for personal use only, may not be redistributed, and may not be used commercially
without prior written permission from the author. This repository only contains the
deployment definition for it and is not a fork of the application. TeamSpeak is a trademark
of TeamSpeak Systems GmbH and Discord of its respective owner; this project is not affiliated
with either.

No icon is shipped with this repository; see [`assets/ICON_SOURCE.md`](assets/ICON_SOURCE.md).
