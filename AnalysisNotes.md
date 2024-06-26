# Analysis notes

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
