# Add Blueprint to Blueprint Page

This repository contains a **Blueprint** designed for use with [Krateo PlatformOps](https://krateo.io), specifically targeting the **Composable Portal** feature.

## Purpose

The Blueprint enables users to **add a Blueprint to the *Blueprint* page** of the Krateo Composable Portal, making it accessible for self-service provisioning by developers and platform consumers.

## What It Does

- Registers a new Blueprint in the Krateo *Blueprint* page
- Makes the Blueprint selectable and usable via the Composable Portal UI
- Supports namespace scoping and user permissions via RBAC (if configured)

## Usage

### Install the Helm Chart

Download Helm Chart values:

```sh
helm repo add marketplace https://marketplace.krateo.io
helm repo update marketplace
helm inspect values marketplace/portal-blueprint-page --version 0.0.1 > ~/portal-blueprint-page-values.yaml
```

Modify the *portal-blueprint-page-values.yaml* file as the following example:

```yaml
blueprint:
  version: 0.0.1 # this is the Blueprint version
title: Frontend Scaffolding (GitHub.com)
icon:
  name: fa-cubes
```

Install the Blueprint using, as a release name, the *Blueprint* name (the Helm Chart name of the blueprint):

```sh
helm install <blueprint-name> template \
  --repo https://marketplace.krateo.io \
  --namespace <blueprint-namespace> \
  --create-namespace \
  -f ~/portal-blueprint-page-values.yaml
  --version 0.0.1 \
  --wait
```

### Install using Krateo Composable Operation

Install the CompositionDefinition for the *Blueprint*:

```sh
cat <<EOF | kubectl apply -f -
apiVersion: core.krateo.io/v1alpha1
kind: CompositionDefinition
metadata:
  name: portal-blueprint-page-blueprint
  namespace: krateo-system
spec:
  chart:
    repo: portal-blueprint-page
    url: https://marketplace.krateo.io
    version: 0.0.1
EOF
```

Install the Blueprint using, as metadata.name, the *Blueprint* name (the Helm Chart name of the blueprint):

```sh
apiVersion: composition.krateo.io/v0-0-1
kind: Portalblueprintpage
metadata:
  name: <blueprint-name>
  namespace: <blueprint-namespace> 
spec:
  blueprint:
    version: 0.0.1 # this is the Blueprint version
  title: Frontend Scaffolding (GitHub.com)
  icon:
    name: fa-cubes
```