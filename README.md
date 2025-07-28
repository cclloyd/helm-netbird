<p align="center">
  <img width="234" src="https://github.com/netbirdio/netbird/raw/main/docs/media/logo-full.png" style="vertical-align: middle; margin: 0 1.5rem" />
  <img src="https://github.com/kubernetes/kubernetes/raw/master/logo/logo.png" width="60" style="vertical-align: middle;" />
</p>


# Netbird Helm Chart

This chart provides a means of deploying Netbird to kubernetes.


---

# Minimal Setup

To use the minimal setup, you will require

- A working kubernetes cluster
- A default storage class with space to provision 2 volume claims
- A worker node which you can port forward traffic to
- A valid hostname and the ability to access it via HTTPS
- A working Auth provider (default config is for Keycloak)

1.  Fill out required config
    ```yaml
    global:
      domain: netbird.example.com
      external_ip: 198.51.100.42
      https: true  # Highly recommended you access via https
    auth:
      client: 'your client'
      secret: 'your client secret'
      ...any remaining auth config
    ```
2. Ensure coturn ports (default `49160-49200`) are being forwarded to the node the pod will be running on.
   - Recommend you set `coturn.affinity.nodeAffinity` so that you can ensure it's always running on a specific node.
3. Run `helm install ./ netbird -f my_values.yaml`
4. Once it's done setting itself, up, access it at your external URL.  The first user to login (redirected from the auth provider) will be set as an admin.
    

> NOTE: The `coturn` pod run with `networkMode: host` since it requires a large range of ports.  This needs to be accessible from outside the cluster.  The easiest way is to set the coturn service type as `LoadBalancer` and forward traffic for coturn port ranges to the load balancer address.




---

# Full Configuration

## Global Settings

| config                            | description                                            | default                |
|-----------------------------------|--------------------------------------------------------|------------------------|
| global.namespace                  | Namespace for Netbird                                  | `'netbird'`            |
| global.domain                     | Domain name used for access (e.g. netbird.example.com) | `''`                   |
| global.https                      | Enables HTTPS for external connections                 | `false`                |
| global.external_ip                | External IP to use                                     | `''`                   |
| global.auth.provider              | OIDC compliant auth provider                           | `'keycloak'`           |
| global.auth.issuer                | Auth issuer URL                                        | `''`                   |
| global.auth.admin                 | Keycloak admin URL                                     | `''`                   |
| global.auth.extra_scopes          | Extra OAuth2 scopes                                    | `'offline_access api'` |
| global.auth.client                | OAuth2 client                                          | `''`                   |
| global.auth.secret                | OAuth2 secret                                          | `''`                   |
| global.auth.audience              | OAuth2 audience                                        | `''`                   |
| global.auth.username_claim        | Claim for username in JWT                              | `'preferred_username'` |
| global.auth.backend.client        | Backend OAuth2 client                                  | `''`                   |
| global.auth.backend.secret        | Backend OAuth2 secret                                  | `''`                   |
| global.auth.backend.audience      | Backend OAuth2 audience                                | `''`                   |
| global.auth.backend.extra_scopes  | Backend extra OAuth2 scopes                            | `'offline_access api'` |
| global.dashboard.port             | Dashboard HTTP port                                    | `80`                   |
| global.management.port            | Management HTTP port                                   | `80`                   |
| global.relay.port                 | Relay port                                             | `33080`                |
| global.relay.secret               | Relay secret                                           | `''`                   |
| global.signal.port                | Signal HTTP port                                       | `80`                   |
| global.coturn.port                | Coturn port                                            | `3478`                 |
| global.coturn.secret              | Coturn secret                                          | `''`                   |
| global.coturn.min_port            | Coturn minimum UDP port                                | `49160`                |
| global.coturn.max_port            | Coturn maximum UDP port                                | `49200`                |
| global.ingress.enabled            | Enable ingress for service                             | `false`                |
| global.ingress.className          | Ingress class name                                     | `''`                   |
| global.ingress.annotations        | Ingress annotations                                    | `{}`                   |
| global.ingress.extra_hosts        | Extra ingress hosts                                    | `[]`                   |
| global.serviceAccount.create      | Create service account                                 | `true`                 |
| global.serviceAccount.automount   | Auto-mount service account                             | `true`                 |
| global.serviceAccount.annotations | Service account annotations                            | `{}`                   |
| global.serviceAccount.name        | Service account name                                   | `""`                   |

## Component Specific Settings

| config                                 | description                      | default                  |
|----------------------------------------|----------------------------------|--------------------------|
| dashboard.image.repository             | Dashboard image repository       | `'netbirdio/dashboard'`  |
| dashboard.image.tag                    | Dashboard image tag              | `'v2.14.0'`              |
| dashboard.image.pullPolicy             | Image pull policy                | `'IfNotPresent'`         |
| dashboard.annotations                  | Pod annotations                  | `{}`                     |
| dashboard.labels                       | Pod labels                       | `{}`                     |
| dashboard.nodeSelector                 | Node selector                    | `{}`                     |
| dashboard.tolerations                  | Tolerations array                | `[]`                     |
| dashboard.affinity                     | Affinity rules                   | `{}`                     |
| dashboard.replicaCount                 | Replica count                    | `1`                      |
| dashboard.resources                    | Resource limits/requests         | `{}`                     |
| dashboard.livenessProbe                | Liveness probe settings          |                          |
| dashboard.readinessProbe               | Readiness probe settings         |                          |
| dashboard.service.type                 | Dashboard service type           | `'ClusterIP'`            |
| dashboard.extra_volumes                | Additional volumes               | `[]`                     |
| dashboard.extra_volumeMounts           | Additional volume mounts         | `[]`                     |
| **Management**                         |                                  |                          |
| management.image.repository            | Management image repository      | `'netbirdio/management'` |
| management.image.tag                   | Management image tag             | `'0.51.2'`               |
| management.image.pullPolicy            | Image pull policy                | `'IfNotPresent'`         |
| management.annotations                 | Pod annotations                  | `{}`                     |
| management.labels                      | Pod labels                       | `{}`                     |
| management.nodeSelector                | Node selector                    | `{}`                     |
| management.tolerations                 | Tolerations                      | `[]`                     |
| management.affinity                    | Affinity rules                   | `{}`                     |
| management.replicaCount                | Replica count                    | `1`                      |
| management.resources                   | Resource limits/requests         | `{}`                     |
| management.livenessProbe               | Liveness probe settings          |                          |
| management.readinessProbe              | Readiness probe settings         |                          |
| management.service.type                | Service type                     | `'ClusterIP'`            |
| management.extra_volumes               | Additional volumes               | `[]`                     |
| management.extra_volumeMounts          | Additional volume mounts         | `[]`                     |
| management.extra_args                  | Additional CLI arguments         | `[]`                     |
| management.persistence.enabled         | Enable persistence               | `true`                   |
| management.persistence.storageClass    | Storage class name               |                          |
| management.persistence.volumeName      | Name of persistent volume        | `'data'`                 |
| management.persistence.existingClaim   | Use existing PVC                 | `''`                     |
| management.persistence.mountPath       | Where to mount storage           | `'/var/lib/netbird'`     |
| management.persistence.configMountPath | Where to mount config            | `'/etc/netbird'`         |
| management.persistence.subPath         | SubPath for mount                | `''`                     |
| management.persistence.accessModes     | AccessModes list                 | `[ReadWriteOnce]`        |
| management.persistence.size            | Requested disk size              | `'1Gi'`                  |
| management.persistence.volumeMode      | Volume mode                      |                          |
| management.persistence.annotations     | Volume/PVC annotations           | `{}`                     |
| management.persistence.labels          | Volume/PVC labels                | `{}`                     |
| management.persistence.selector        | Selector map for PVC             | `{}`                     |
| management.persistence.dataSource      | PVC data source                  | `{}`                     |
| **Relay**                              |                                  |                          |
| relay.image.repository                 | Relay image repository           | `'netbirdio/relay'`      |
| relay.image.tag                        | Relay image tag                  | `'0.51.2'`               |
| relay.image.pullPolicy                 | Image pull policy                | `'IfNotPresent'`         |
| relay.annotations                      | Pod annotations                  | `{}`                     |
| relay.labels                           | Pod labels                       | `{}`                     |
| relay.nodeSelector                     | Node selector                    | `{}`                     |
| relay.tolerations                      | Tolerations                      | `[]`                     |
| relay.affinity                         | Affinity rules                   | `{}`                     |
| relay.replicaCount                     | Replica count                    | `1`                      |
| relay.resources                        | Resource limits/requests         | `{}`                     |
| relay.service.type                     | Service type                     | `'ClusterIP'`            |
| relay.extra_volumes                    | Additional volumes               | `[]`                     |
| relay.extra_volumeMounts               | Additional volume mounts         | `[]`                     |
| relay.extra_env                        | Additional environment variables | `{}`                     |
| **Signal**                             |                                  |                          |
| signal.image.repository                | Signal image repository          | `'netbirdio/signal'`     |
| signal.image.tag                       | Signal image tag                 | `'0.51.2'`               |
| signal.image.pullPolicy                | Image pull policy                | `'IfNotPresent'`         |
| signal.annotations                     | Pod annotations                  | `{}`                     |
| signal.labels                          | Pod labels                       | `{}`                     |
| signal.nodeSelector                    | Node selector                    | `{}`                     |
| signal.tolerations                     | Tolerations                      | `[]`                     |
| signal.affinity                        | Affinity rules                   | `{}`                     |
| signal.replicaCount                    | Replica count                    | `1`                      |
| signal.resources                       | Resource limits/requests         | `{}`                     |
| signal.livenessProbe                   | Liveness probe settings          |                          |
| signal.readinessProbe                  | Readiness probe settings         |                          |
| signal.service.type                    | Service type                     | `'ClusterIP'`            |
| signal.extra_volumes                   | Additional volumes               | `[]`                     |
| signal.extra_volumeMounts              | Additional volume mounts         | `[]`                     |
| signal.persistence.enabled             | Enable persistence               | `true`                   |
| signal.persistence.storageClass        | Storage class name               |                          |
| signal.persistence.volumeName          | Persistent volume name           | `'data'`                 |
| signal.persistence.existingClaim       | Use existing PVC                 | `''`                     |
| signal.persistence.mountPath           | Mount path                       | `'/var/lib/netbird'`     |
| signal.persistence.subPath             | SubPath for mount                | `''`                     |
| signal.persistence.accessModes         | List of accessModes              | `[ReadWriteOnce]`        |
| signal.persistence.size                | Requested disk size              | `'1Gi'`                  |
| signal.persistence.volumeMode          | Volume mode                      |                          |
| signal.persistence.annotations         | PVC annotations                  | `{}`                     |
| signal.persistence.labels              | PVC labels                       | `{}`                     |
| signal.persistence.selector            | PVC selector                     | `{}`                     |
| signal.persistence.dataSource          | Data source                      | `{}`                     |
| signal.persistence.overrideVolume      | Override volume                  |                          |
| signal.persistence.overrideVolumeMount | Override volume mount            |                          |
| **Coturn**                             |                                  |                          |
| coturn.image.repository                | Coturn image repository          | `'coturn/coturn'`        |
| coturn.image.tag                       | Coturn image tag                 | `'4.7.0'`                |
| coturn.image.pullPolicy                | Image pull policy                | `'IfNotPresent'`         |
| coturn.annotations                     | Pod annotations                  | `{}`                     |
| coturn.labels                          | Pod labels                       | `{}`                     |
| coturn.nodeSelector                    | Node selector                    | `{}`                     |
| coturn.tolerations                     | Tolerations                      | `[]`                     |
| coturn.affinity                        | Affinity rules                   | `{}`                     |
| coturn.replicaCount                    | Replica count                    | `1`                      |
| coturn.resources                       | Resources for pods               | `{}`                     |
| coturn.livenessProbe                   | Liveness probe settings          |                          |
| coturn.readinessProbe                  | Readiness probe settings         |                          |
| coturn.service.type                    | Service type                     | `'ClusterIP'`            |
| coturn.service.annotations             | Service annotations              | `{}`                     |
| coturn.extra_volumes                   | Additional volumes               | `[]`                     |
| coturn.extra_volumeMounts              | Additional volume mounts         | `[]`                     |

