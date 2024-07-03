# Issues and changes

- [X] liquidsoap not connecting to api or icecast. {Port number issue for api. Revert static ports!}
- [ ] Add clusterIssuer to helm chart: FAIL, related to other cluster-issuer issues
- [ ] No README.md file appearing on artefact hub for charts.
- [ ] Repeated issues with resolving gb.archive.ubuntu.com, preventing apt update / install etc.
- [ ] Microk8s versioning
- [ ] Cert manager repo issues
- [ ] Ubuntu auto-updates, and kernel kernel `5.15.0-101-generic`
- [X] `/main` and `/stream1` use same username:password
- [X] Chart dev turnaround
- [X] Stream intros path issue
- [ ] 

## Changes

### V0.3.X Dev Version

- Backup cronjob
- Restore values file option on chart deploy
- Library scan cronjob

### V0.4.X

- Security: BOM report items
- Security: Network Scan
- Security: Password management {cluster managed passwords, helm chart password lookup}

#### Note on process

- Versioning noise, to be mitigated by local chart dev (outside of helm chart publishing, and flux)
- Local dev to use dedicated hardware , with flux pulling from local cluster and chart repos.
- Branch chart for dev.
- Dedicated, public flux repo for substrate dependencies, and chart deployment demo
- Cluster pre-existing secrets for all sensitive information, flux helper functions to manage secrets in an aggregated process.
- Dedicated hardware: VM on workstation / as mac mini T630 ase not operational
- For config fine tuning: fine-tune manifests on deployment cluster, and copy final change-set back to helm chart after stabilisation.

### Local helm chart development

Testing turn-around for chart changes, for flux deployment.

- To force chart renewal, and verify the installed manifest set, provide minor version change for the chart {helm package, helm repo index, publish}
- Force pull of new chart by update chart release version, and commit.
- Monitor helm release on k8s cluster for helm chart issues, fix and repeat
- When helm release passes syntactic testing and deployment, monitor pods for issues, fix and repeat

For faster development to test turnaround, skip versioning, packaging, index, publishing, and gitops:

- Undeploy radio app in flux
- Deploy via dev workstation local helm api, with local paths intact:

```bash
helm install -f /Users/martincolley/workspace/podzonedev-gitops/radio.thruhere.net/radio-values.yaml --namespace radio radio /Users/martincolley/workspace/libretime-helm/charts/libretime
```

## Cluster substrate build

### Microk8s versioning

- <https://microk8s.io/docs/getting-started> mentions install: `sudo snap install microk8s --classic --channel=1.30`
- See: <https://microk8s.io/docs/setting-snap-channel>
- Microk8s stable 1.28, 1.29, and 1.30 include V1.8 packages, e.g. <quay.io/jetstack/cert-manager-controller:v1.8.0>
- Cert manager 1.8 was last modified 2 yrs ago, and is now at version 1.15.1.
- Failure with both microk8s add on, and helm chart implementation using reference flux example, no problem not restricted to microk8s:

```log
Failed to pull image "quay.io/jetstack/cert-manager-cainjector:v1.8.0":
 failed to pull and unpack image "quay.io/jetstack/cert-manager-cainjector:v1.8.0":
 failed to resolve reference "quay.io/jetstack/cert-manager-cainjector:v1.8.0": 
 failed to do request: Head "https://quay.io/v2/jetstack/cert-manager-cainjector/manifests/v1.8.0": 
 net/http: TLS handshake timeout
```

- Practical maintenance of microk8s clusters <https://github.com/cert-manager/cert-manager/issues/3104>

```text
Practically, as soon as the admin enables an addon he is expected to own its maintenance. 
We are offering new versions of the addon enable/disable scripts and in order to get them 
the admin would need to microk8s addons repo update <repo>
```

- Microk8s add-on issues: Values used in add-on enablement not available in basic documentation, but with some scratching:
- <https://github.com/canonical/microk8s-addons-repo-template/tree/main>
- Ref: <https://github.com/canonical/microk8s-core-addons/issues/255>
- So the challenge is to find source of installed base, and manage using microk8s add-ons, or to manage using flux.

References:

- More research required:
- <https://github.com/artifacthub/hub/blob/master/docs/metadata/artifacthub-repo.yml>
- <https://helm.sh/docs/topics/charts/>
- <https://microk8s.io/docs/setting-snap-channel>
- <https://microk8s.io/docs/install-alternatives>
- <https://github.com/canonical/microk8s-addons-repo-template/>
- <https://microk8s.io/docs/how-to-manage-addons>
- <https://discuss.kubernetes.io/t/how-is-quality-assured-in-the-microk8s-project/28654>
- <https://discuss.kubernetes.io/t/microk8s-installation-failing-on-ubuntu-22-10/28578/3>
- <https://github.com/canonical/microk8s/issues/4361>
- <https://github.com/canonical/microk8s-core-addons/issues/255>
- <https://discuss.kubernetes.io/t/addon-cert-manager/21122/12>
- <https://github.com/cert-manager/cert-manager/releases>
- 

### Ubuntu updates

T630 do not boot successfully with `Ubuntu, with Linux 5.15.0-112-generic`, but kernel `5.15.0-101-generic` does work.

To prevent auto updates installing on new devices installed from installer 22.04.3, (with network disconnected):

- Stop auto updates: `sudo systemctl stop unattended-upgrades`
- Remove auto updates: `sudo apt-get purge unattended-upgrades`
- Install known good kernel: `sudo apt install linux-image-5.15.0-58-generic linux-headers-5.15.0-58-generic`
- Check which images are grub options: `sudo view /boot/grub/grub.cfg`
- Edit: `GRUB_DEFAULT=your_kernel_name_from_grub.cfg`
- Update grub: `sudo update-grub`

References:

- <https://askubuntu.com/questions/1186492/terminate-unattended-upgrades-or-whatever-is-using-apt-in-ubuntu-18-04-or-later>
- <https://askubuntu.com/questions/1404722/downgrade-kernel-for-ubuntu-22-04-lts>
- <https://unix.stackexchange.com/questions/465201/how-do-i-roll-back-to-a-previous-ubuntu-kernel-running-ubuntu-16-04>
- <https://askubuntu.com/questions/100232/how-do-i-change-the-grub-boot-order/110738#110738>
- <https://gist.github.com/jfeilbach/f4d0b19df82e04bea8f10cdd5945a4ff?permalink_comment_id=5000464>

### Resolving gb.archive.ubuntu.com

- <https://askubuntu.com/questions/91543/apt-get-update-fails-to-fetch-files-temporary-failure-resolving-error>
- <https://raspberrypi.stackexchange.com/questions/40687/raspberry-pi-install-screen-internal-error-ordering-was-unable-to-handle-the>

### Cert manager repo issues

- One of many issues reported: <https://github.com/cert-manager/cert-manager/issues/3104>
- <https://github.com/cert-manager/cert-manager/issues/6557>
- Repo source clarity issues:
  - Built into charts:  <quay.io/jetstack/>
  - From github issues: <hub.helm.sh/charts/jetstack/cert-manager/>
- Resolution: Shift all add-ons to flux, Use repo: <https://artifacthub.io/packages/helm/cert-manager/cert-manager>
- Root Cause Issue: cert-manager helm repo fails intermittently

## Icecast externalised configuration

Notes:

- `mount-name` requires leading slash, e.g. `/stream1`
- Error: `[2024-07-03  11:30:53] WARN source/source_apply_mount Cannot open intro file "/usr/share/icecast/web/stream1Intro.mpg": No such file or directory`

- Snapshot: 3 July 2024

```xml
Name:         icecast-conf
Namespace:    radio
Labels:       app.kubernetes.io/managed-by=Helm
              helm.toolkit.fluxcd.io/name=radio
              helm.toolkit.fluxcd.io/namespace=radio
Annotations:  meta.helm.sh/release-name: radio-radio
              meta.helm.sh/release-namespace: radio

Data
====
icecast.xml:
----
<icecast>
    <location>UK</location>
    <admin>motttt@muso.radio</admin>
    <hostname>broadcast.muso.club</hostname>
    <fileserve>1</fileserve>
    <limits>
        <clients>100</clients>
        <sources>6</sources>
        <queue-size>524288</queue-size>
        <client-timeout>30</client-timeout>
        <header-timeout>15</header-timeout>
        <source-timeout>10</source-timeout>
        <burst-size>65535</burst-size>
    </limits>
    <authentication>
        <source-password>l18r3t1meSource</source-password>
        <relay-password>l18r3t1meRelay</relay-password>
        <admin-user>admin</admin-user>
        <admin-password>l18r3t1meAdmin</admin-password>
    </authentication>
    <listen-socket>
        <port>8000</port> 
    </listen-socket>
    <http-headers>
        <header type="cors" name="Access-Control-Allow-Origin" />
        <header type="cors" name="Access-Control-Allow-Headers" />
        <header type="cors" name="Access-Control-Expose-Headers" />
    </http-headers>
    <paths>
        <basedir>/usr/share/icecast</basedir>
        <logdir>/usr/share/icecast/log</logdir>
        <webroot>/web</webroot>
        <adminroot>/usr/share/icecast/admin</adminroot>
        <reportxmldb>/usr/share/icecast/report-db.xml</reportxmldb>
        <alias source="/" destination="/status.xsl"/>
    </paths>
    <logging>
        <accesslog>-</accesslog>
        <errorlog>-</errorlog>
        <loglevel>4</loglevel>
    </logging>
    <security>
        <chroot>false</chroot>
        <tls-context>
        </tls-context>
    </security>
    <mount type="normal">
        <mount-name>/stream1</mount-name>
        <username>stream1</username>
        <password>stream1Password</password>
        <intro>stream1Intro.mpg</intro>
        <stream-name>MusoClubStream01</stream-name>
        <stream-description>Stream 1 on Muso Club Radio, a PodZone Project</stream-description>
        <stream-url>https://broadcast.muso.club</stream-url>
    </mount>
    <mount type="normal">
        <mount-name>/stream2</mount-name>
        <username>stream2</username>
        <password>stream2Pa$$word</password>
        <intro>stream2Intro.mpg</intro>
        <fallback-mount>/main</fallback-mount>
        <stream-name>MusoClubStream01</stream-name>
        <stream-description>Stream 2 on Muso Club Radio, a PodZone Project</stream-description>
        <stream-url>https://broadcast.muso.club</stream-url>
    </mount>
</icecast>


BinaryData
====

Events:  <none>
```
