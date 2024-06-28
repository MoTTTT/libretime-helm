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
- Continue to use Values, but provide template values override the excludes noise.

## Dependencies

The **Cluster Substrate** is an environmental dependency for the repeatable functioning of the libretime helm chart. Testing of the chart has been done on the following environment:

- Minimum server configuration: Single node k8s "cluster".
- Operating system: Ubuntu 22.04 minimised.
- Kubernetes storage volumes: MicroCeph. Used for the postgres database in the libretime use-case.
- NFS storage for media input, media storage, and database backups.
- Microk8s {channel 1.30/stable, channel 1.30/stable}, with the following add-ons enabled and configured:
  - `metrics-server`: For exposing cpu and ram usage metrics.
  - `cert-manager`: For providing https certificates to the cluster.
  - `metallb`: A cluster load balancer for traffic from the Internet, from a port forwarded on the Internet gateway.
  - `rook-ceph`: For supporting ceph storage to the cluster.
  - `connect-external-ceph`: This is a microk8s command that can be run once the *rook-ceph* add-on is enabled, to enable the consumption of the external Microceph volume for the database.
  - `ingress-nginx`: For supporting cluster level listeners for Internet traffic.

### Repeatable automated build from Bare Metal on a unit scale

The choice of repeatability of deployment from *Bare Metal* is driven by the interest in self-hosted solutions. With the growing acceptance of Kubernetes as a generalised computing substrate, coupled with support in the Kubernetes ecosystem for GitOps, there is opportunity for abstracting entire solutions into a single set of files, in a naturally change-controlled way.

If the cluster is bootstrapped with a flex repo after a clean Microk8s install, all the above configuration can be managed using GitOps. For the dev environment build, the public repo at <https://github.com/MoTTTT/podzonedev-gitops.git> can be cloned and adjusted for local off cluster infrastructure dependencies before bootstrapping a cluster with flex.

The notion of bootstrapping the cluster for GitOps before any add-ons or configuration is attractive. So the candidate gitops repo can be cloned to provide the minimum configuration repeatable install from bare metal on a unit scale, that configures the environment, and then deploys the radio chart.

Scaling out involves joining nodes to the cluster using microk8s CLI, but a design goal is for a single node cluster to work for the purposes of ensuring an efficient, repeatable development, quality assurance, or production environment.
