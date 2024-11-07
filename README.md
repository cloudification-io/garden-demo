# shoot-saas

## Contents

- [shoot-saas](#shoot-saas)
  - [Contents](#contents)
  - [Overview](#overview)
    - [Example Shoot spec](#example-shoot-spec)

## Overview

This project contains ready to deploy templates for Shoot cluster(s) with following aaS software:
1. Gitlab-CE
2. PSQL Operator
3. RabbitMQ Operator

### Example Shoot spec

```yaml
  extensions:
  - type: shoot-flux
    providerConfig:
      apiVersion: flux.extensions.gardener.cloud/v1alpha1
      kind: FluxConfig
      flux:
        version: v2.4.0
        registry: ghcr.io/fluxcd
        namespace: flux-system
      source:
        template:
          apiVersion: source.toolkit.fluxcd.io/v1
          kind: GitRepository
          spec:
            ref:
              tag: v2.0.0
            # secretRef:
            #  name: flux-system
            url: https://github.com/cloudification-io/garden-demo.git
        # secretResourceName: flux-git-token
      kustomization:
        template:
          apiVersion: kustomize.toolkit.fluxcd.io/v1
          kind: Kustomization
          spec:
            path: ./infrastructure/flux
            prune: true
            patches:
              ## Optional apps - uncomment what you need
            - target:
                kind: Kustomization
                name: psql
              patch: |
                - op: replace
                  path: /spec/suspend
                  value: false
            # - target:
            #     kind: Kustomization
            #     name: gitlab
            #   patch: |
            #     - op: replace
            #       path: /spec/suspend
            #       value: false
            # - target:
            #     kind: Kustomization
            #     name: rabbitmq
            #   patch: |
            #     - op: replace
            #       path: /spec/suspend
            #       value: false


  ## Include this when using a private repo
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
