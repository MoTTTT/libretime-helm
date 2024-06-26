# Analysis notes

## Strategies

- Internalise (unexposed as values) configuration items that are not cosmetic, and to which solution is critically sensitive. (Opinionated)
- Full set of components for a working solution, with clear cluster dependencies
- Documented, clear, repeatable, automated deploy, from hardware level.

## Factoids

- Default root icecast directory for libretime docker image is: </usr/share/icecast>

## Storage

Mount analysis from v 0.0.3

- analyzer: /srv/libretime from libretime-storage (rw)
- api: /srv/libretime from libretime-storage (rw)
- icecast: none: need mounted directory for mount fallback tracks
- liquidsoap-playout: /app from libretime-playout (rw)
- postgress: none???
- rabitmq: none
- web copy-files: /appdata from libretime-assets (rw)
- web legacy: /srv/libretime from libretime-storage(rw)
- web legacy: /tmp from libretime-storage (rw,path=".tmp")
- web nginx: /srv/libretime from libretime-storage (ro)
- worker: none

- libretime-playout: emptyDir; no problem
- libretime-storage/srv/libretime: needs to be rw mounted for analyzer, api, web.legacy, and ro for web.nginx
- libretime-storage/tmp: web.legacy

## Ports

- Regarding as static. Local to k8s namespace, therefore no risk of external clash.
- Include ingress, from Cluster-Issuer up.
- Provide automation for TCP ingress (non http/s)
- nginx: 8080
- api: 9001
- email: 25 (leave exposed in Values, as mail server is an external component)
- postgres: 5432
- rabbitmq: 5672
- icecast: 8000
- legacy: 9000
