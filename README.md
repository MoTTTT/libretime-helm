# LibreTime Helm Chart

This repo is forked from the public repo at <https://github.com/unbelauscht/libretime-helm/>, which does not have a licence file, so hope that's ok.

It has been adjusted to use nginx as ingress, rather than traefik, because nginx ingress is already in use.

Chart.yaml has also been updated to refer to this repo, and myself as maintainer.

## Notes

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

## Original README.md

### Installation & Configuration

This is an example `values.yaml` file with parameters you may want to modify.

It's recommended to change anything that looks like a password.

```yaml
# Volume to store your uploaded music
volumes:
  music: |
    - name: libretime-storage # don't change the volume name
      nfs:
        server: nas
        path: /LibretimeMusic

# Can also be a PVC, just make sure it exists.
# volumes:
#   music: |
#     - name: libretime-storage
#       persistentVolumeClaim:
#         claimName: libretime-storage

publicUrl: https://radio.example.com:443
publicHost: radio.example.net
streamUrl: https://stream.example.com/main
streamHost: stream.example.com

api:
  config:
    general:
      api_key: libretime
      secret_key: libretime
      allowed_cors_origins:
        - "https://publicUrl"
postgres:
  external: false
  secret:
    existingSecret: "" # prod: set the name of your secret to keep your data safe
    # or change these values
    username: libretime
    password: libretime
  persistence:
    existingClaim: "" # prod: set the name of your PVC to keep your data
rabbitmq:
  secret:
    existingSecret: "" # prod: set the name of your secret to keep your data safe
    username: libretime
    password: libretime
icecast:
  stream:
    name: Libretime.fm
    mountpoint: main
    description: Libretime.fm
    website: https://radio.example.com
    genre: "Libretime.fm"
    passwords:
      source: libretime
      admin: libretime
      relay: libretime
  ingress:
    auth:
      enabled: false # requires traefik
      username: listener
      password: libretime
```