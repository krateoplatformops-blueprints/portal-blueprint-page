# Template Blueprint: Add Composition to Template Page

This repository contains a **Blueprint** designed for use with [Krateo PlatformOps](https://krateo.io), specifically targeting the **Composable Portal** feature.

## Purpose

The Blueprint enables users to **add a Composition to the Template page** of the Krateo Composable Portal, making it accessible for self-service provisioning by developers and platform consumers.

## What It Does

- Registers a new Composition in the Krateo Template page
- Makes the Composition selectable and usable via the Composable Portal UI
- Supports namespace scoping and user permissions via RBAC (if configured)

## Usage

### Install the Helm Chart

Download Helm Chart values:

```sh
helm repo add marketplace https://marketplace.krateo.io
helm repo update marketplace
helm inspect values marketplace/template --version 0.0.1 > ~/template-values.yaml
```

Modify the *template-values.yaml* file as the following example:

```yaml
composition:
  version: 0.0.1 # this is the Composition version
title: Frontend Scaffolding (GitHub.com)
icon:
  name: fa-cubes
```

Install the Blueprint using, as a release name, the *Composition* name (the Helm Chart name of the composition):

```sh
helm install <composition-name> template \
  --repo https://marketplace.krateo.io \
  --namespace <composition-namespace> \
  --create-namespace \
  -f ~/template-values.yaml
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
  name: template-blueprint
  namespace: krateo-system
spec:
  chart:
    repo: template
    url: https://marketplace.krateo.io
    version: 0.0.1
EOF
```

Install the Blueprint using, as metadata.name, the *Composition* name (the Helm Chart name of the composition):

```sh
apiVersion: composition.krateo.io/v0-0-1
kind: Template
metadata:
  name: <composition-name> 
  namespace: <composition-namespace> 
spec:
  composition:
    version: 0.0.1 # this is the Composition version
  title: Frontend Scaffolding (GitHub.com)
  icon:
    name: fa-cubes
```