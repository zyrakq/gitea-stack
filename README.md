# 🐳 Gitea Docker Stack

This project contains a collection of Docker configurations and compose files for running Gitea and related services.

## 🧩 Components

### 🔐 SSL Automation

#### [🔒 Let's Encrypt Manager](modules/ssl-automation/letsencrypt-manager)

Automatic SSL certificate management from Let's Encrypt for production deployments. Provides seamless HTTPS integration for Docker containers using nginx-proxy and acme-companion.
[Learn more about Let's Encrypt Manager configuration](modules/ssl-automation/letsencrypt-manager/README.md).

#### [🏠 Step CA Manager](modules/ssl-automation/step-ca-manager)

Local domain stack with trusted self-signed certificates for virtual network deployments. Includes private CA management and local DNS resolution for development environments.
[Learn more about Step CA Manager configuration](modules/ssl-automation/step-ca-manager/README.md).

### 🔑 Identity Management

#### [🔐 Keycloak](modules/identity-management/keycloak)

Enterprise-grade identity and access management solution. Provides authentication, authorization, and user management for secure application access.
[Learn more about Keycloak configuration](modules/identity-management/keycloak/README.md).

For Gitea integration, see: [Keycloak Integration](https://docs.gitea.io/en-us/usage/authentication/)

#### [🔐 Kanidm](modules/identity-management/kanidm)

Modern identity and access management server with comprehensive authentication capabilities. Provides secure identity management with modular configuration system and multiple deployment modes.
[Learn more about Kanidm configuration](modules/identity-management/kanidm/README.md).

## 🌐 Services

### [🍵 Gitea](app)

Gitea — a lightweight DevOps platform providing Git hosting, issue tracking, and CI/CD capabilities.
[Learn more about Gitea configuration](app/README.md).

## 🚀 Getting Started

To run the services, use the appropriate `docker-compose.yml` files in the subprojects. Make sure all environment variables are configured correctly.

Each service directory contains:

- 📋 Docker Compose configurations
- 🔧 Environment variable examples
- 📖 Detailed setup instructions
- 🛠️ Helper scripts for development and production

## 🏗️ Project Structure

```sh
├── modules/
│   ├── ssl-automation/      # SSL certificate automation
│   │   ├── letsencrypt-manager/ # Let's Encrypt SSL certificate management
│   │   └── step-ca-manager/     # Local CA and trusted certificates
│   ├── identity-management/ # Identity and access management
│   │   └── keycloak/        # Keycloak identity provider
│   └── gitea/               # Main Gitea service configs
```

## 📄 License

This project is dual-licensed under:

- [Apache License 2.0](LICENSE-APACHE)
- [MIT License](LICENSE-MIT)
