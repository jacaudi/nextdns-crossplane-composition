# NextDNS Crossplane Composition

This project provides a Crossplane Composition and CompositeResourceDefinition (XRD) for managing NextDNS profiles using Crossplane and the provider-http with Go templating.

## Overview
- **Crossplane** enables you to declaratively manage cloud and external resources using Kubernetes CRDs.
- **provider-http** allows Crossplane to manage any resource with an HTTP API.
- **function-go-templating** enables dynamic resource generation using Go templates in Crossplane compositions.
- This composition lets you manage NextDNS profiles as Kubernetes resources, mapping your desired state to the NextDNS API.

## How It Works
- You define a `Profile` custom resource (CR) in Kubernetes, specifying your NextDNS profile configuration.
- The composition uses Go templating to generate an HTTP `Request` resource, which interacts with the NextDNS API to create, update, and delete profiles.
- Secrets and status are injected back into Kubernetes for use by other resources or applications.
- See `example/nextdnsprofile/claim.yaml` for a sample Profile resource.

## Installation

### Prerequisites

1. **Kubernetes Cluster:**
   Ensure you have a Kubernetes cluster running (e.g., Minikube, Kind, EKS, GKE).
    - v1.29+ recommended
2. **Install Crossplane:**
   Follow the [official docs](https://docs.crossplane.io/v1.20/getting-started/).

### Composition Installation Steps (Configuration Package)

Create a `Configuration` resource referencing your published package:
```yaml
apiVersion: pkg.crossplane.io/v1
kind: Configuration
metadata:
  name: nextdns-crossplane-composition
spec:
  package: ghcr.io/jacaudi/nextdns-crossplane-composition:v1.0.0
```
Apply it:
```sh
kubectl apply -f configuration.yaml
```

### Configuration

1. **Create the provider config:**
   ```sh
   kubectl apply -f example/nextdnsprofile/providerconfig.yaml
   ```

2. **Create a secret with your NextDNS API key:**
   ```sh
   kubectl create secret generic nextdns-api-key \
     --from-literal=api-key=<YOUR_NEXTDNS_API_KEY> \
     -n crossplane-system
   ```

3. **Apply a Profile claim:**

   ```sh
   kubectl apply -f example/nextdnsprofile/claim.yaml
   ```

### 5. Verify Installation

Check that the package, XRD, and Composition are installed:
```sh
kubectl get xrd
kubectl get composition
```

## Troubleshooting
- Ensure all Crossplane pods, provider-http, and function-go-templating pods are running in the `crossplane-system` namespace.
- Check CRD status with `kubectl get crd profiles.nextdns.crossplane.io`.
- Check resource status with `kubectl describe profile <name>`.
- Review provider and function logs for errors.

## References
- [Crossplane Documentation](https://docs.crossplane.io/v1.20/)
- [provider-http](https://github.com/crossplane-contrib/provider-http)
- [function-go-templating](https://github.com/crossplane-contrib/function-go-templating)
- [NextDNS API Docs](https://nextdns.io/developer)
