# Docker compose stack with NGINX reverse proxy serving Whoami webapp with TLS termination
## What it does
A `whoami` test container sits behind an `nginx` reverse proxy.
Nginx terminates TLS with a self-signed certificate and forwards
plain HTTP traffic internally to whoami over a private Docker network.
## Architecture
Browser (client) -> (HTTPS:6767) -> nginx -> (HTTP:80, internal network) -> whoami
## Requirements
-Docker
-Docker Compose
-OpenSSL
NOTE: Although this will run on Docker Desktop it is recommended to use a Linux machine.
## Setup
**Running the app**
docker compose up -d
**Stopping / cleaning up**
docker compose down
## File overview
docker-compose.yaml   - Defines the two services (whoami, nginx), their shared
                        network, port mapping, and volume mounts

nginx.conf            - nginx config: terminates TLS on 443, proxies decrypted
                        traffic to whoami:80 internally

secrets/site.crt       - Self-signed TLS certificate (gitignored, not committed)

secrets/site.key       - Private key for the certificate (gitignored, not committed)

.gitignore             - Excludes secrets/ from version control
## Notes / things intentionally left out
- No ssl_ciphers hardening line, kept for a simple working baseline first.
- Self-signed cert, not trusted publicly.
## Troubleshooting
**Check logs:** docker logs <container> or docker compose logs <service>
**Check container status:** docker ps (running) / docker ps -a (all)
**Validate config:** docker compose config
**Verify files on disk:** ls -la, cat <file>, pwd
**Check for hidden whitespace/indentation issues:** cat -A <file>
**Isolate the failing layer:** YAML -> nginx config -> networking -> app
**Restart clean:** docker compose down && docker compose up -d
***Always confirm host vs. container ports/paths match***
