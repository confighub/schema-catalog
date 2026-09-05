# schema-catalog

JSON schemas for Kubernetes custom resource types that the
[CRDs-catalog](https://github.com/datreeio/CRDs-catalog) does not carry.

It is a supplement, not a replacement. ConfigHub's `vet-schemas` tries the upstream
Kubernetes schemas first, then the CRDs-catalog, then this repository, so a type only needs a
schema here if neither of the first two has one. Anything that lands upstream should be removed
from here.

## Layout

The same layout and filename convention as the CRDs-catalog, so the same kubeconform schema
location template addresses both:

```
<api group>/<lowercase kind>_<version>.json
```

```
https://raw.githubusercontent.com/confighub/schema-catalog/main/{{.Group}}/{{.ResourceKind}}_{{.ResourceAPIVersion}}.json
```

## What is here, and why

| Group | Types | Why |
| --- | --- | --- |
| `elbv2.services.k8s.aws` | Listener, LoadBalancer, Rule, TargetGroup | The CRDs-catalog carries `elbv2.k8s.aws` (AWS Load Balancer Controller) and `elbv2.aws.upbound.io` (Crossplane), but not the ACK group. |
| `eks.services.k8s.aws` | Capability | Absent from the catalog's `eks.services.k8s.aws`. |
| `iam.services.k8s.aws` | ServiceLinkedRole | Absent from the catalog's `iam.services.k8s.aws`. |
| `rds.services.k8s.aws` | DBClusterEndpoint | Absent from the catalog's `rds.services.k8s.aws`. |
| `hub.traefik.io` | AccessControlPolicy | The catalog carries `traefik.io` and `traefik.containo.us`, but not the Traefik Hub group. |

## Regenerating

Each schema is generated from the type's upstream CustomResourceDefinition with
[openapi2jsonschema-go](https://github.com/yannh/kubeconform/tree/master/openapi2jsonschema-go),
which is what produces the CRDs-catalog's own files, so the output has the same shape:

```sh
git clone https://github.com/yannh/kubeconform
cd kubeconform/openapi2jsonschema-go && go build -o o2j .

# from the CRD, into the group's directory
FILENAME_FORMAT='{kind}_{version}' ./o2j <crd>.yaml
```

Sources for the CRDs here:

- ACK: `https://raw.githubusercontent.com/aws-controllers-k8s/<service>-controller/main/helm/crds/<group>_<plural>.yaml`
- Traefik Hub: `https://raw.githubusercontent.com/traefik/traefik-helm-chart/master/traefik/crds/hub.traefik.io_<plural>.yaml`

Check the version before adding one. Several types ConfigHub used to declare turned out to name
API versions upstream does not serve, and a schema that can never be fetched is how that was
noticed.

## License

MIT, per `LICENSE`. The schemas are derived from each project's own CustomResourceDefinitions and
carry their upstream licenses.
