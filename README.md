
# gke-argocd

![Version: 0.1.0](https://img.shields.io/badge/Version-0.1.0-informational?style=flat-square) ![Type: application](https://img.shields.io/badge/Type-application-informational?style=flat-square) ![AppVersion: 0.1.0](https://img.shields.io/badge/AppVersion-0.1.0-informational?style=flat-square)

## Description

ArgoCD on GKE with integrated Google Cloud optimisations and security.

## Usage

### Add Helm repository

```shell
helm repo add gke-argocd https://braveokafor.github.io/gke-argocd/
helm repo update
```

### Install Helm chart

Using default configurations:

```bash
helm install --generate-name gke-argocd/gke-argocd
```

Customising configurations:

```bash
helm install --generate-name --set ingress.enabled=true, gke-argocd/gke-argocd
```

## Configuration

The following table lists the configurable parameters of the gke-argocd chart and their default values.

## Values

| Key | Type | Default | Description |
|-----|------|---------|-------------|
| argo-cd.additionalLabels | object | `{"app.kubernetes.io/part-of":"argocd"}` | Additional Labels for Argo CD |
| argo-cd.configs | object | `{"cm":{"admin.enabled":false,"dex.config":"connectors:\n- config:\n    issuer: https://accounts.google.com\n    clientID: $gke-argocd-oauth-secret:client_id\n    clientSecret: $gke-argocd-oauth-secret:client_secret\n  type: oidc\n  id: google\n  name: Google\n","url":"http://argocd.example.com"},"rbac":{"policy.csv":"g, user@example.com, role:admin\n","policy.default":"role:readonly","scopes":"[groups, email]"}}` | Configurations for Argo CD components |
| argo-cd.configs.cm."admin.enabled" | bool | `false` | Enable or disable admin account |
| argo-cd.configs.cm."dex.config" | string | `"connectors:\n- config:\n    issuer: https://accounts.google.com\n    clientID: $gke-argocd-oauth-secret:client_id\n    clientSecret: $gke-argocd-oauth-secret:client_secret\n  type: oidc\n  id: google\n  name: Google\n"` | Dex configuration for OIDC |
| argo-cd.configs.cm.url | string | `"http://argocd.example.com"` | Argo CD URL |
| argo-cd.configs.rbac."policy.csv" | string | `"g, user@example.com, role:admin\n"` | CSV for RBAC policies |
| argo-cd.configs.rbac."policy.default" | string | `"role:readonly"` | Default RBAC policy |
| argo-cd.configs.rbac.scopes | string | `"[groups, email]"` | RBAC scopes |
| argo-cd.dex | object | `{"servicePortHttpName":"tcp"}` | Dex service port configuration |
| argo-cd.repoServer | object | `{"envFrom":[{"secretRef":{"name":"argocd-vault-plugin-credentials"}}],"extraContainers":[{"command":["/var/run/argocd/argocd-cmp-server"],"image":"quay.io/argoproj/argocd:v2.6.7","name":"avp","securityContext":{"runAsNonRoot":true,"runAsUser":999},"volumeMounts":[{"mountPath":"/var/run/argocd","name":"var-files"},{"mountPath":"/home/argocd/cmp-server/plugins","name":"plugins"},{"mountPath":"/tmp","name":"cmp-tmp"},{"mountPath":"/home/argocd/cmp-server/config/plugin.yaml","name":"cmp-plugin","subPath":"avp.yaml"},{"mountPath":"/usr/local/bin/argocd-vault-plugin","name":"custom-tools","subPath":"argocd-vault-plugin"}]},{"command":["/var/run/argocd/argocd-cmp-server"],"image":"quay.io/argoproj/argocd:v2.6.7","name":"avp-helm","securityContext":{"runAsNonRoot":true,"runAsUser":999},"volumeMounts":[{"mountPath":"/var/run/argocd","name":"var-files"},{"mountPath":"/home/argocd/cmp-server/plugins","name":"plugins"},{"mountPath":"/tmp","name":"cmp-tmp"},{"mountPath":"/home/argocd/cmp-server/config/plugin.yaml","name":"cmp-plugin","subPath":"avp-helm.yaml"},{"mountPath":"/usr/local/bin/argocd-vault-plugin","name":"custom-tools","subPath":"argocd-vault-plugin"}]},{"command":["/var/run/argocd/argocd-cmp-server"],"image":"quay.io/argoproj/argocd:v2.6.7","name":"avp-kustomize","securityContext":{"runAsNonRoot":true,"runAsUser":999},"volumeMounts":[{"mountPath":"/var/run/argocd","name":"var-files"},{"mountPath":"/home/argocd/cmp-server/plugins","name":"plugins"},{"mountPath":"/tmp","name":"cmp-tmp"},{"mountPath":"/home/argocd/cmp-server/config/plugin.yaml","name":"cmp-plugin","subPath":"avp-kustomize.yaml"},{"mountPath":"/usr/local/bin/argocd-vault-plugin","name":"custom-tools","subPath":"argocd-vault-plugin"}]}],"initContainers":[{"args":["curl -L https://github.com/argoproj-labs/argocd-vault-plugin/releases/download/v$(AVP_VERSION)/argocd-vault-plugin_$(AVP_VERSION)_linux_amd64 -o argocd-vault-plugin && chmod +x argocd-vault-plugin && mv argocd-vault-plugin /custom-tools/\ncurl -L https://github.com/mikefarah/yq/releases/download/v${YQ_VERSION}/yq_linux_amd64 -o yq && chmod +x yq && mv yq /custom-tools/"],"command":["sh","-c"],"env":[{"name":"AVP_VERSION","value":"1.16.1"},{"name":"YQ_VERSION","value":"4.35.2"}],"image":"registry.access.redhat.com/ubi8","name":"download-tools","volumeMounts":[{"mountPath":"/custom-tools","name":"custom-tools"}]}],"podAnnotations":{"traffic.sidecar.istio.io/excludeOutboundPorts":"443"},"rbac":[{"apiGroups":[""],"resources":["secrets","configmaps"],"verbs":["get","list","watch"]}],"serviceAccount":{"annotations":{"iam.gke.io/gcp-service-account":"argocd-sa@example-project.iam.gserviceaccount.com"},"automountServiceAccountToken":true},"volumes":[{"configMap":{"name":"cmp-plugin"},"name":"cmp-plugin"},{"emptyDir":{},"name":"cmp-tmp"},{"emptyDir":{},"name":"custom-tools"}]}` | Repo Server configurations |
| argo-cd.repoServer.envFrom | list | `[{"secretRef":{"name":"argocd-vault-plugin-credentials"}}]` | Argo Vault Plugin configurations |
| argo-cd.repoServer.extraContainers | list | `[{"command":["/var/run/argocd/argocd-cmp-server"],"image":"quay.io/argoproj/argocd:v2.6.7","name":"avp","securityContext":{"runAsNonRoot":true,"runAsUser":999},"volumeMounts":[{"mountPath":"/var/run/argocd","name":"var-files"},{"mountPath":"/home/argocd/cmp-server/plugins","name":"plugins"},{"mountPath":"/tmp","name":"cmp-tmp"},{"mountPath":"/home/argocd/cmp-server/config/plugin.yaml","name":"cmp-plugin","subPath":"avp.yaml"},{"mountPath":"/usr/local/bin/argocd-vault-plugin","name":"custom-tools","subPath":"argocd-vault-plugin"}]},{"command":["/var/run/argocd/argocd-cmp-server"],"image":"quay.io/argoproj/argocd:v2.6.7","name":"avp-helm","securityContext":{"runAsNonRoot":true,"runAsUser":999},"volumeMounts":[{"mountPath":"/var/run/argocd","name":"var-files"},{"mountPath":"/home/argocd/cmp-server/plugins","name":"plugins"},{"mountPath":"/tmp","name":"cmp-tmp"},{"mountPath":"/home/argocd/cmp-server/config/plugin.yaml","name":"cmp-plugin","subPath":"avp-helm.yaml"},{"mountPath":"/usr/local/bin/argocd-vault-plugin","name":"custom-tools","subPath":"argocd-vault-plugin"}]},{"command":["/var/run/argocd/argocd-cmp-server"],"image":"quay.io/argoproj/argocd:v2.6.7","name":"avp-kustomize","securityContext":{"runAsNonRoot":true,"runAsUser":999},"volumeMounts":[{"mountPath":"/var/run/argocd","name":"var-files"},{"mountPath":"/home/argocd/cmp-server/plugins","name":"plugins"},{"mountPath":"/tmp","name":"cmp-tmp"},{"mountPath":"/home/argocd/cmp-server/config/plugin.yaml","name":"cmp-plugin","subPath":"avp-kustomize.yaml"},{"mountPath":"/usr/local/bin/argocd-vault-plugin","name":"custom-tools","subPath":"argocd-vault-plugin"}]}]` | Extra containers in the repo server |
| argo-cd.repoServer.extraContainers[0] | object | `{"command":["/var/run/argocd/argocd-cmp-server"],"image":"quay.io/argoproj/argocd:v2.6.7","name":"avp","securityContext":{"runAsNonRoot":true,"runAsUser":999},"volumeMounts":[{"mountPath":"/var/run/argocd","name":"var-files"},{"mountPath":"/home/argocd/cmp-server/plugins","name":"plugins"},{"mountPath":"/tmp","name":"cmp-tmp"},{"mountPath":"/home/argocd/cmp-server/config/plugin.yaml","name":"cmp-plugin","subPath":"avp.yaml"},{"mountPath":"/usr/local/bin/argocd-vault-plugin","name":"custom-tools","subPath":"argocd-vault-plugin"}]}` | ArgoCD Vault Plugin container configuration |
| argo-cd.repoServer.initContainers | list | `[{"args":["curl -L https://github.com/argoproj-labs/argocd-vault-plugin/releases/download/v$(AVP_VERSION)/argocd-vault-plugin_$(AVP_VERSION)_linux_amd64 -o argocd-vault-plugin && chmod +x argocd-vault-plugin && mv argocd-vault-plugin /custom-tools/\ncurl -L https://github.com/mikefarah/yq/releases/download/v${YQ_VERSION}/yq_linux_amd64 -o yq && chmod +x yq && mv yq /custom-tools/"],"command":["sh","-c"],"env":[{"name":"AVP_VERSION","value":"1.16.1"},{"name":"YQ_VERSION","value":"4.35.2"}],"image":"registry.access.redhat.com/ubi8","name":"download-tools","volumeMounts":[{"mountPath":"/custom-tools","name":"custom-tools"}]}]` | Initialization containers |
| argo-cd.repoServer.podAnnotations | object | `{"traffic.sidecar.istio.io/excludeOutboundPorts":"443"}` | Pod annotations |
| argo-cd.repoServer.rbac | list | `[{"apiGroups":[""],"resources":["secrets","configmaps"],"verbs":["get","list","watch"]}]` | RBAC configurations for the repo server |
| argo-cd.repoServer.serviceAccount | object | `{"annotations":{"iam.gke.io/gcp-service-account":"argocd-sa@example-project.iam.gserviceaccount.com"},"automountServiceAccountToken":true}` | Service account configurations |
| argo-cd.repoServer.volumes | list | `[{"configMap":{"name":"cmp-plugin"},"name":"cmp-plugin"},{"emptyDir":{},"name":"cmp-tmp"},{"emptyDir":{},"name":"custom-tools"}]` | Volume configurations |
| argo-cd.server | object | `{"extraArgs":["--insecure"],"service":{"annotations":{"cloud.google.com/backend-config":"{\"default\": \"gke-argocd-backend-config\"}","cloud.google.com/neg":"{\"ingress\": true}"}}}` | Server configurations |
| argo-cd.server.extraArgs | list | `["--insecure"]` | Additional arguments for the server |
| argo-cd.server.service | object | `{"annotations":{"cloud.google.com/backend-config":"{\"default\": \"gke-argocd-backend-config\"}","cloud.google.com/neg":"{\"ingress\": true}"}}` | Service annotations |
| argo-cd.server.service.annotations."cloud.google.com/backend-config" | string | `"{\"default\": \"gke-argocd-backend-config\"}"` | Backend configuration for GCP |
| argo-cd.server.service.annotations."cloud.google.com/neg" | string | `"{\"ingress\": true}"` | Network Endpoint Groups (NEG) configuration |
| avp.enabled | bool | `true` | Enabled |
| iap.client_id | string | `"abcde12345.apps.googleusercontent.com"` | Client id |
| iap.client_secret | string | `"abcde12345"` | Client secret |
| iap.enabled | bool | `false` | Enabled |
| ingress.annotations | object | `{"kubernetes.io/ingress.global-static-ip-name":"argocd-public-ip"}` | Annotations configurations |
| ingress.annotations."kubernetes.io/ingress.global-static-ip-name" | string | `"argocd-public-ip"` | Kubernetes.io/ingress.global-static-ip-name |
| ingress.enabled | bool | `false` | Enabled |
| ingress.hosts | list | `[{"host":"argocd.example.com","paths":["/"],"serviceName":"argocd-server"}]` | Hosts |
| ingress.tls | list | `[]` | Tls |
| oauth.client_id | string | `"abcde12345.apps.googleusercontent.com"` | Client id |
| oauth.client_secret | string | `"abcde12345"` | Client secret |
| oauth.enabled | bool | `false` | Enabled |

**Homepage:** <https://github.com/braveokafor/gke-argocd/>

## Source Code

* <https://github.com/braveokafor/gke-argocd/>

## Maintainers

| Name | Email | Url |
| ---- | ------ | --- |
| braveokafor | <okaforbrave@gmail.com> | <https://www.linkedin.com/in/braveokafor/> |
