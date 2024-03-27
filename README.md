# LibreTime Helm Chart

This repo is forked from the public repo at <https://github.com/unbelauscht/libretime-helm/>, which does not have a licence file, so hope that's ok.

It has been adjusted to use nginx as ingress, rather than traefik, because nginx ingress is already in use.

Chart.yaml has also been updated to refer to this repo, and myself as maintainer.

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