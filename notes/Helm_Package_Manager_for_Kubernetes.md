# Helm - Package Manager for Kubernetes

## What is Helm?

**Helm** is the package manager for Kubernetes, similar to how **APT** is used on Debian-based Linux distributions and **YUM/DNF** is used on Red Hat-based distributions.

Helm simplifies the deployment and management of Kubernetes applications by packaging Kubernetes manifests into reusable units called **Charts**.

### Benefits of Helm

- Simplifies application deployment on Kubernetes.
- Packages multiple Kubernetes YAML manifests into a single deployable unit.
- Enables versioning of Kubernetes applications.
- Supports application upgrades and rollbacks.
- Promotes reuse and standardization across environments.
- Allows sharing applications through public or private repositories.

## Helm Charts

A **Helm Chart** is a collection of files that describe a Kubernetes application.

Think of a chart as a packaged application that can be deployed repeatedly across different Kubernetes clusters.

### A Helm Chart Contains

- Kubernetes resource definitions (Deployments, Services, ConfigMaps, Secrets, etc.)
- Configuration values
- Templates
- Metadata about the application

### Using Helm Charts

You can:

- Create and publish your own Helm Charts.
- Store charts in private repositories for internal use.
- Download and deploy community-maintained charts from public repositories.

Many popular applications provide official Helm Charts, including:

- MySQL
- PostgreSQL
- MongoDB
- Redis
- Prometheus
- Grafana
- Elasticsearch

Search available charts:

```bash
helm search repo <keyword>
```

Example:

```bash
helm search repo nginx
```

## Public and Private Helm Repositories

Helm Charts are distributed through repositories.

### Public Repositories

Accessible to everyone.

Examples:

- Bitnami
- Artifact Hub
- Prometheus Community Charts

### Private Repositories

Used within organizations to distribute internal applications.

Benefits:

- Secure application distribution.
- Version-controlled deployments.
- Standardized deployment patterns.
- Centralized application management.

# Helm Templating Engine

One of Helm's most powerful features is its templating engine.

Instead of maintaining separate YAML files for every environment, Helm allows you to create reusable templates with dynamic values.

### Example

Template (`templates/deployment.yaml`):

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: {{ .Values.appName }}
```

Values (`values.yaml`):

```yaml
appName: my-nginx-app
```

Rendered Output:

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: my-nginx-app
```

## Why Use Templates?

Templates enable organizations to create a common deployment blueprint for multiple applications.

Benefits include:

- Reduced YAML duplication.
- Standardized deployments.
- Easier maintenance.
- Environment-specific customization.

This is particularly useful in a microservices architecture where dozens or hundreds of applications share similar deployment patterns.

## values.yaml

The `values.yaml` file stores configurable values used by Helm templates.

Example:

```yaml
replicaCount: 2

image:
  repository: nginx
  tag: latest

service:
  type: ClusterIP
  port: 80
```

These values are injected into template files when the chart is installed or upgraded.

## Overriding Values

Values defined in `values.yaml` are considered default values.

They can be overridden using either a custom values file or command-line arguments.

### Using a Custom Values File

```bash
helm install my-release my-chart \
  --values my-values.yaml
```

Example `my-values.yaml`:

```yaml
replicaCount: 5

image:
  tag: stable
```

### Using the --set Flag

```bash
helm install my-release my-chart \
  --set replicaCount=5
```

This approach is convenient for overriding a small number of values during deployment.

## Deploying Across Multiple Environments

One of the most common use cases for Helm is deploying the same application to different environments while using different configuration values.

Directory Example:

```text
values-dev.yaml
values-staging.yaml
values-prod.yaml
```

Deploy to Development:

```bash
helm install my-app my-chart \
  --values values-dev.yaml
```

Deploy to Staging:

```bash
helm install my-app my-chart \
  --values values-staging.yaml
```

Deploy to Production:

```bash
helm install my-app my-chart \
  --values values-prod.yaml
```

This allows a single chart to be reused across all environments while keeping environment-specific settings separate.

# Helm Chart Structure

A typical Helm chart has the following directory structure:

```text
mychart/
│
├── Chart.yaml
├── values.yaml
├── charts/
├── templates/
└── README.md
```

## Chart.yaml

The `Chart.yaml` file contains metadata about the chart.

Example:

```yaml
apiVersion: v2
name: mychart
description: Sample Helm Chart
version: 1.0.0
appVersion: "1.0"
```

Typical information includes:

- Chart name
- Description
- Chart version
- Application version
- Dependency information

## values.yaml

Contains the default configuration values used by templates.

Example:

```yaml
replicaCount: 2

image:
  repository: nginx
  tag: latest
```

These values can be overridden during installation or upgrades.


## charts/

The `charts/` directory stores chart dependencies.

Example:

```text
charts/
├── mysql
└── redis
```

Dependencies can also be declared in `Chart.yaml` and automatically downloaded.


## templates/

The `templates/` directory contains Kubernetes manifest templates.

Examples:

```text
templates/
├── deployment.yaml
├── service.yaml
├── configmap.yaml
├── secret.yaml
├── ingress.yaml
└── pvc.yaml
```

Helm replaces template placeholders with values during installation.

# Installing a Helm Chart

Install a chart:

```bash
helm install my-release my-chart
```

During installation:

1. Helm reads the chart.
2. Helm loads values from `values.yaml`.
3. Templates are rendered.
4. Kubernetes resources are created.
5. A release is recorded in Helm.

## Viewing Rendered Templates

To preview generated manifests without deploying them:

```bash
helm template my-release my-chart
```

This is useful for troubleshooting and validating configurations.


## Dry Run Installation

To simulate an installation:

```bash
helm install my-release my-chart --dry-run
```

Benefits:

- Validate templates.
- Verify values.
- Identify configuration errors before deployment.


# Release Management

One of Helm's most valuable features is its built-in release management.

Whenever a chart is installed, Helm creates a **Release**.

A release tracks:

- Installed resources
- Applied configuration values
- Revision history
- Upgrade history
- Rollback points


## View Installed Releases

```bash
helm list
```

Example Output:

```text
NAME         NAMESPACE   REVISION
my-release   default     1
```


## Install a Release

```bash
helm install my-release my-chart
```

## Upgrade a Release

Update an existing deployment:

```bash
helm upgrade my-release my-chart
```

Example:

```bash
helm upgrade my-release my-chart \
  --values prod-values.yaml
```

## View Release History

```bash
helm history my-release
```

Example Output:

```text
REVISION    STATUS       CHART
1           deployed     nginx-1.0.0
2           deployed     nginx-1.1.0
3           deployed     nginx-1.2.0
```

## Roll Back a Release

Revert to a previous release version:

```bash
helm rollback my-release 2
```

Benefits:

- Fast recovery from failed deployments.
- Minimal downtime.
- Safe and repeatable application updates.

## Uninstall a Release

Remove a deployed application:

```bash
helm uninstall my-release
```

This removes all Kubernetes resources associated with the release.

# Why Helm is Important

Managing Kubernetes applications manually often involves maintaining dozens or even hundreds of YAML manifest files.

Helm simplifies this process by providing:

- Application packaging
- Reusable templates
- Centralized configuration management
- Dependency management
- Version control
- Environment-specific customization
- Upgrade and rollback capabilities
- Simplified Kubernetes deployments

For these reasons, Helm has become one of the most widely adopted tools in the Kubernetes ecosystem.


# Helm Workflow Example

The following diagram shows a typical Helm workflow:

```text
Chart Templates + values.yaml
            |
            v
       helm install
            |
            v
     Render Templates
            |
            v
   Kubernetes Resources
            |
            v
      Helm Release
```

# Summary

Helm is the package manager for Kubernetes that simplifies the deployment and management of applications through reusable **Charts**. By combining templating, centralized configuration, dependency management, and release tracking, Helm enables teams to deploy applications consistently across development, staging, and production environments.

### Key Takeaways

- Helm packages Kubernetes applications into reusable Charts.
- Charts contain templates, configuration values, and metadata.
- Templates support dynamic configuration using `values.yaml`.
- Charts can be shared through public or private repositories.
- Helm simplifies multi-environment deployments.
- Release management enables upgrades, rollbacks, and version tracking.
- Helm is a critical tool for managing Kubernetes applications at scale.