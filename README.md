# LibreTime Helm Chart

This repo is forked from the public repo at <https://github.com/unbelauscht/libretime-helm/>, which does not have a licence file, so hope that's ok. I have not previously done an installation of libretime with all it's components, so this chart was invaluable to me as a starting point.

The original README.md file has been retained, with some notes on the top, as READMEOriginal.md.

## Features

- Uses the libretime docker images unmodified, including the icecast one. Libretime rocks!
- Automated, scheduled backup of the libretime database
- Optional database restore, as an alternative for `libretime-api migrate` during deployment
- Ingresses and certificates for web component (legacy and api) and icecast
- Library directory scanning and upload, using `libretime-api bulk_import`
- Configurable Icecast, for decoupling incoming streams from libretime, and configuring fallback etc.

## Dependencies

I have tested this installation on a self-hosted microk8s cluster. The following section describes the deployment environment:

### Storage

NFS volumes for:

- `/srv/libretime`
- backup
- Audio track library

Ceph-rbd volume for:

- postgres data volume

### k8s configuration

- Microk8s installed: `sudo snap install microk8s --channel=1.29/stable --classic`. Microk8s rocks!
- Microk8s addons installed: cert-manager, rook-ceph,  metrics-server (optional), metallb
- Microk8s connect-external-ceph, connecting to a self-hosted ceph cluster. Ceph rocks!

### k8s infrastructure assets

- ingress-nginx: adding values to support TCP ingress for icecast streams is optional, but http/s ingresses use `ingressClassName: nginx`
- Cluster-issuer: This supports the ingress annotation `cert-manager.io/cluster-issuer: lets-encrypt`
- Adminer: Ths is a db access tool. Installation is not required for the chart to function. Adminer is useful for understanding the db schema, and doing analysis of loaded tracks etc.

### GitOps

I am using gitops to install this helm chart, and the assets listed above. GitOps rocks!

The flux source repo for the dev installation is here: https://github.com/MoTTTT/podzonedev-gitops. Flex rocks!

### Deployment environment

- DEV: 2 X HP T630s with 24GB RAM
- Ubuntu 22.04 minimised
- microk8s installed and clustered
- ISP Router configured with port-forwarding for http/s ingress, and icecast protocol TCP ports.
- Production: The production environment has three k8s nodes, but is otherwise the same.
