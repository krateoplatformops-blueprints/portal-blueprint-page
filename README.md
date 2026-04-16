# Add Blueprint to *Portal Blueprint Page*

This repository contains a **Blueprint** designed for use with [Krateo PlatformOps](https://krateo.io), specifically targeting the **Composable Portal** feature.

## Purpose

The Blueprint enables users to **add a Blueprint to the *Blueprint* page** of the Krateo Composable Portal, making it accessible for self-service provisioning by developers and platform consumers.

## What It Does

- Registers a new Blueprint in the Krateo *Blueprint* page
- Makes the Blueprint selectable and usable via the Composable Portal UI
- Supports namespace scoping and  user permissions via RBAC (if configured)

## Installation

There are two ways to install this Blueprint:

- [1. Helm Chart](#method-1-helm-chart) 
- [2. Krateo Composable Operation](#method-2-krateo-composable-operation)

---

### Method 1: Helm Chart

> Note: For the time being this installation method may not work, refer to the alternative installation method '[2. Krateo Composable Operation](#method-2-krateo-composable-operation)' below.

**Step 1: Download the default Helm chart values**

```sh
helm repo add marketplace https://marketplace.krateo.io
helm repo update marketplace
helm inspect values marketplace/portal-blueprint-page --version 1.0.6 > ~/portal-blueprint-page-values.yaml
```

**Step 2: Edit the values file**

Modify the `~/portal-blueprint-page-values.yaml` as shown below:

```yaml
blueprint:
  repo: github-scaffolding
  url: https://marketplace.krateo.io
  version: 1.0.0 # this is the Blueprint version
  hasPage: false
form:
  alphabeticalOrder: false
panel:
  title: GitHub Scaffolding
  icon:
    name: fa-cubes
```

**Step 3: Install the blueprint**

When installing the chart, ensure the release name matches the `blueprint.repo` defined above:

```sh
helm install github-scaffolding portal-blueprint-page \
  --repo https://marketplace.krateo.io \
  --namespace demo-system \
  --create-namespace \
  -f ~/portal-blueprint-page-values.yaml \
  --version 1.0.6 \
  --wait
```

---

### Method 2: Krateo Composable Operation

**Step 1: Install the `CompositionDefinition`**

```sh
cat <<EOF | kubectl apply -f -
apiVersion: core.krateo.io/v1alpha1
kind: CompositionDefinition
metadata:
  name: portal-blueprint-page
  namespace: krateo-system
spec:
  chart:
    repo: portal-blueprint-page
    url: https://marketplace.krateo.io
    version: 1.0.6
EOF
```

As a result of this command:
- The core-provider generates a new Custom Resource Definition (CRD) based on the `portal-blueprint-page` chart's schema.
- Resources of kind `PortalBlueprintPage` (with `apiVersion: composition.krateo.io/v1-0-6`) can now be created in the cluster.

Next, create the Composition custom resource.

> Note: `metadata.name` and `spec.blueprint.repo` must be the same.

```sh
cat <<EOF | kubectl apply -f -
apiVersion: composition.krateo.io/v1-0-6
kind: PortalBlueprintPage
metadata:
  name: github-scaffolding
  namespace: demo-system
spec:
  blueprint:
    repo: github-scaffolding
    url: https://marketplace.krateo.io
    version: 1.0.0 # this is the Blueprint version
    hasPage: false
  form:
    alphabeticalOrder: false
  panel:
    title: GitHub Scaffolding
    icon:
      name: fa-cubes
EOF
```

As a result of this command:
- A new `PortalBlueprintPage` Composition is created, which deploys the portal widgets and generates a new `CompositionDefinition` for the `github-scaffolding` chart using the values provided in `spec.blueprint`.
- A blueprint card is created in the Krateo Portal's "Blueprints" section, allowing users to configure and deploy this blueprint directly from the UI.