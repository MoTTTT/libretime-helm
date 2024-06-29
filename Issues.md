# Issues and changes

- [X] liquidsoap not connecting to api or icecast. {Port number issue for api. Revert static ports!}

## Changes

- [ ] Add clusterIssuer to helm chart??

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

- `Failed to pull image "quay.io/jetstack/cert-manager-cainjector:v1.8.0": failed to pull and unpack image "quay.io/jetstack/cert-manager-cainjector:v1.8.0":  │
│ failed to resolve reference "quay.io/jetstack/cert-manager-cainjector:v1.8.0": failed to do request: Head "https://quay.io/v2/jetstack/cert-manager-cainjector/manifests/v1.8.0": net/http: TLS handshake timeout`
- <https://github.com/cert-manager/cert-manager/issues/3104>
- < https://hub.helm.sh/charts/jetstack/cert-manager/> vs <quay.io/jetstack/>
- Canonical position: `Practically, as soon as the admin enables an addon he is expected to own its maintenance. We are offering new versions of the addon enable/disable scripts and in order to get them the admin would need to microk8s addons repo update <repo>` 
- Ref: <https://github.com/canonical/microk8s-core-addons/issues/255>
- Resolution: Shift all add-ons to flux, Use repo: <https://artifacthub.io/packages/helm/cert-manager/cert-manager>
