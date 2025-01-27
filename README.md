# Crewstand Grafana

- [Crewstand Grafana](#crewstand-grafana)
  - [Project Description](#project-description)
  - [Installation Instructions](#installation-instructions)
    - [Prerequisites](#prerequisites)
    - [Docker Installation](#docker-installation)
  - [Accessing Grafana](#accessing-grafana)
  - [Configuration Steps](#configuration-steps)
    - [Environment Variables](#environment-variables)
  - [Dashboard Provisioning](#dashboard-provisioning)
    - [Dashboards](#dashboards)
    - [Datasources](#datasources)
  - [Automated Workflows for Package Registry](#automated-workflows-for-package-registry)
    - [Preferred Method for Publishing Dashboard Changes](#preferred-method-for-publishing-dashboard-changes)

## Project Description

Crewstand Grafana is a containerized Grafana implementation designed to run as a microservice within the Crewstand project. It comes pre-configured with automated dashboard provisioning and InfluxDB data source setup, requiring minimal configuration through environment variables.

## Installation Instructions

### Prerequisites

- Docker
- InfluxDB instance
- InfluxDB access token

### Docker Installation

1. Pull the container image:
   ```bash
   docker pull ghcr.io/felizcoder/crewstand.grafana:latest
   ```
2. Run the container with required environment variables:

```bash
docker run -d \
  -p 3000:3000 \
  -e INFLUX_URL=your_influxdb_url \
  -e INFLUX_TOKEN=your_influxdb_token \
  ghcr.io/felizcoder/crewstand.grafana:latest
```

## Accessing Grafana

Once the container is running:

Access the Grafana interface at http://localhost:3000
All pre-configured dashboards will be automatically available
The InfluxDB data source will be configured and ready to use

## Configuration Steps

### Environment Variables

The following environment variables are required:

| Environment Variable | Description                              |
| -------------------- | ---------------------------------------- |
| INFLUX_URL           | URL of your InfluxDB instance            |
| INFLUX_TOKEN         | Authentication token for InfluxDB access |

To [edit dashboards](#dashboards) you may set

| Environment Variable       | Description |
| -------------------------- | ----------- |
| GF_AUTH_ANONYMOUS_ORG_ROLE | Editor      |

## Dashboard Provisioning

### Dashboards

Dashboards are automatically provisioned from the repository's [`dashboards`](provisioning\dashboards) directory. Key features of the dashboard provisioning:

- Dashboards are read from files automatically
- UI updates are disabled to maintain consistency
- Folder structure is preserved from the filesystem

You can add new dashboards by placing them in the `dashboards` directory and ensuring they follow the naming convention `dashboard_name.json`.

The easiest way to edit dashboards is to use the Grafana web interface. To do this, start the container with the environment variable `GF_AUTH_ANONYMOUS_ORG_ROLE=Editor`. Now you can edit and add dashboards directly from the web interface. On save grafana will show you the new `.json` file, you can copy the content of the file and paste it into the `dashboard_name.json` file in the `dashboards` directory.

### Datasources

The InfluxDB data source is configured in the repository's [`datasource.yml`](provisioning\datasources\datasources.yml) file. It needs the environment variables `INFLUX_URL` and `INFLUX_TOKEN` to be set correctly.

New data sources can be added by modifying the `datasource.yml` file.

## Automated Workflows for Package Registry

For convenience and consistency, this repository utilizes GitHub Actions to automate the build, publishing, and release processes. This ensures that the latest changes are always available in the package registry.

- **Docker Image Publishing**: The
  `docker-publish.yml` workflow automatically builds and pushes Docker images to the GitHub Container Registry (ghcr.io) on push events to branches (excluding main) and semver tags.
- **Release Management**: The `release-please.yml` workflow, triggered on pushes to the main branch, automates the release process using googleapis/release-please-action. This ensures that new releases are created whenever significant changes are merged into main.

### Preferred Method for Publishing Dashboard Changes

To publish changes to the dashboards, please follow this preferred workflow:

1. **Commit and Push** your dashboard changes to a `feature branch`.
   - Commit messages must follow the conventional commits format to facilitate automated release management.
   - use a feature or fix commit message format like `feat(dashboard): add new dashboard` or `fix(dashboard): fix dashboard layout`.
2. **Create a Pull Request** to merge your changes into the `main` branch.
3. Once the **PR is merged**, the automated workflows will: - Create a new release (if the merge was into main). - Build and publish a new Docker image (if necessary, based on the branch or tag).
   This approach ensures that all changes are properly tracked, versioned, and made available through the package registry in a consistent and secure manner.
