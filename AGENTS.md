# AGENTS.md

## Cursor Cloud specific instructions

This is a Docker Compose-based FreshRSS deployment (self-hosted RSS aggregator). There is no application source code to build, lint, or test — the entire project is infrastructure/configuration consumed via a Docker image.

### Services

| Service | How to run | Port |
|---------|-----------|------|
| FreshRSS | `sudo docker compose up -d` | `127.0.0.1:8086` |

### Key caveats

- **Docker must be started first**: Run `sudo dockerd &>/tmp/dockerd.log &` and wait a few seconds before running any `docker` commands. The daemon does not auto-start in this environment.
- **Port binding**: The container binds to `127.0.0.1:8086` (localhost only). Access via `http://localhost:8086` from the VM browser.
- **First-run setup**: After `docker compose up -d`, FreshRSS requires initial installation and user creation via CLI before the web UI is usable:
  ```
  sudo docker exec freshrss php /var/www/FreshRSS/cli/do-install.php --default-user admin --base-url http://localhost:8086 --language en --db-type sqlite
  sudo docker exec freshrss php /var/www/FreshRSS/cli/create-user.php --user admin --password 'admin123' --language en
  sudo docker exec freshrss cli/access-permissions.sh
  ```
- **Feed import**: After user creation, import the pre-configured OPML feeds:
  ```
  sudo docker cp feeds.opml freshrss:/tmp/feeds.opml
  sudo docker exec freshrss php /var/www/FreshRSS/cli/import-for-user.php --user admin --filename /tmp/feeds.opml
  sudo docker exec freshrss php /var/www/FreshRSS/cli/actualize-user.php --user admin
  ```
- **No lint/test/build**: This project has no source code, so there are no lint, test, or build steps. The development workflow is: edit `docker-compose.yml` or `feeds.opml`, then `sudo docker compose up -d` to apply changes.
- **Data persistence**: `./data/` and `./extensions/` are Docker volume mounts (gitignored). They are created at runtime and persist across container restarts.
