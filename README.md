# shoot-saas

## Contents

- [shoot-saas](#shoot-saas)
  - [Contents](#contents)
  - [Overview](#overview)
  - [Folders](#folders)
  - [Example Shoot spec](#example-shoot-spec)
  - [Ingress annotations](#ingress-annotations)

## Overview

This project contains ready to deploy templates for Shoot cluster(s) with following aaS software:
1. Gitlab-CE
2. PSQL Operator

## Folders

- `example-curl` - demonstration project
- `gitlab` - base folder for Gitlab
- `psql-operator` - base folder for PSQL operator
- `saas` - this folder contains possible variations of to-be-deployed services:
  - `spec-1` - deploy only Gitlab
  - `spec-2` - deploy only PSQL Operator
  - `spec-3` - deploy only Gitlab + PSQL Operator

This structure can be extended with addiotional Service in future. 

## Example Shoot spec

Spec template

```yaml
---
apiVersion: core.gardener.cloud/v1beta1
kind: Shoot
metadata:
  name: test-1
  namespace: PROJECT_NAMESPACE
spec:
  extensions:
  - type: shoot-flux
    providerConfig:
      apiVersion: flux.extensions.gardener.cloud/v1alpha1
      kind: FluxConfig
      flux:
        # renovate:flux-version
        version: v2.3.0
        registry: ghcr.io/fluxcd
        namespace: flux-system
      source:
        template:
          apiVersion: source.toolkit.fluxcd.io/v1
          kind: GitRepository
          spec:
            ref:
              branch: master
            # secretRef:
            #  name: flux-system
            url: https://github.com/cloudification-io/garden-demo.git
        # secretResourceName: flux-git-token
      kustomization:
        template:
          apiVersion: kustomize.toolkit.fluxcd.io/v1
          kind: Kustomization
          spec:
            path: saas/spec-3

  resources:
  - name: flux-git-token
    resourceRef:
      apiVersion: v1
      kind: Secret
      name: flux-git-token
      # additionalSecretResources:
      #   - name: shoot-vault-secret
      #     targetName: vault
```

## Ingress annotations

```yaml

```
