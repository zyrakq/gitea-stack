# 🍵 Gitea Service

A modular Docker Compose configuration system for Gitea with SQLite backend and support for multiple environments.

## 🚀 Quick Start

### 1. Build Configurations

Generate all Docker Compose configurations using [stackbuilder](https://github.com/zyrakq/stackbuilder):

```bash
sb build
```

This creates ready-to-use configurations in the `build/` directory from source components in `components/`.

### 2. Choose Configuration

Navigate to your desired environment:

```bash
# Development with port forwarding
cd build/forwarding/base/

# DevContainer environment  
cd build/devcontainer/base/

# Production with Let's Encrypt SSL
cd build/letsencrypt/base/

# Production with Step CA SSL
cd build/step-ca/base/
```

### 3. Configure Environment

Copy and customize the environment file:

```bash
cp .env.example .env
# Edit .env with your values
```

### 4. Deploy

Start the services:

```bash
docker compose up --build -d
```

Access: `http://localhost:3000` (for forwarding mode)

## 📁 Directory Structure

- **`build/`** - Generated Docker Compose configurations ready for deployment
- **`components/`** - Source components used to build final configurations
  - `base/` - Core Gitea service configuration
  - `environments/` - Environment-specific configurations (devcontainer, forwarding, letsencrypt, step-ca)
  - `extensions/` - Optional extensions (step-ca-trust)

## 🔧 Available Environments

- **devcontainer**: Development environment with workspace network
- **forwarding**: Development environment with port forwarding (3000, 2222)
- **letsencrypt**: Production with Let's Encrypt SSL certificates
- **step-ca**: Production with Step CA SSL certificates

## 🔧 Environment Variables

### Base Configuration

- `USER_UID`: User ID for Gitea process (default: 1000)
- `USER_GID`: Group ID for Gitea process (default: 1000)
- `GITEA__database__DB_TYPE`: Database type (default: sqlite3)
- `GITEA__service__DISABLE_REGISTRATION`: Disable user registration (default: true)
- `GITEA__service__REQUIRE_SIGNIN_VIEW`: Require sign-in to view (default: true)
- `GITEA__security__INSTALL_LOCK`: Lock installation (default: true)

### First Run Configuration

⚠️ **Important for first deployment:**

To set up the initial administrator account, you have two options:

1. **Registration method**: Set `GITEA__service__DISABLE_REGISTRATION=false` to allow the first registered user to become a superadmin
2. **Installation method**: Set `GITEA__security__INSTALL_LOCK=false` to configure the admin account and other settings through the web installation interface

After initial setup, it's recommended to set both variables back to `true` for security.

### OIDC Configuration

- `GITEA__oauth2__ENABLE`: Enable OAuth2 authentication (default: true)
- `ALLOW_ONLY_EXTERNAL_REGISTRATION`: Allow only external registration (default: false)

**Note**: OIDC providers (like Keycloak) must be configured manually through the Gitea web interface at **Site Administration → Authentication Sources**. The environment variables above only enable the OAuth2 functionality.

### Let's Encrypt Configuration

- `VIRTUAL_PORT`: Port for nginx-proxy (default: 3000)
- `VIRTUAL_HOST`: Domain for nginx-proxy
- `LETSENCRYPT_HOST`: Domain for SSL certificate
- `LETSENCRYPT_EMAIL`: Email for certificate registration

### Step CA Configuration

- `VIRTUAL_PORT`: Port for nginx-proxy (default: 3000)
- `VIRTUAL_HOST`: Domain for nginx-proxy
- `LETSENCRYPT_HOST`: Domain for SSL certificate
- `LETSENCRYPT_EMAIL`: Email for certificate registration
- `STEP_CA_TRUST`: Enable trust for Step CA certificates (default: true, for OIDC integration)

## 🔐 OIDC Integration

### Keycloak Integration

To integrate Gitea with Keycloak for single sign-on:

1. **Enable OIDC in Gitea**: Set `GITEA__oauth2__ENABLE=true` in your `.env` file
2. **Configure Keycloak Client**: Create a new client in Keycloak with:
   - Client ID: `gitea`
   - Client Protocol: `openid-connect`
   - Access Type: `confidential`
   - Valid Redirect URIs: `https://your-gitea-domain/user/oauth2/keycloak/callback`
3. **Configure in Gitea Web Interface**:
   - Go to **Site Administration → Authentication Sources**
   - Add new **OAuth2** authentication source
   - Provider: **OpenID Connect**
   - Client ID: Your Keycloak client ID
   - Client Secret: Your Keycloak client secret
   - OpenID Connect Auto Discovery URL: `https://your-keycloak-domain/realms/your-realm/.well-known/openid_configuration`

**Note**: OIDC configuration must be completed through the Gitea web interface after deployment. Environment variables only enable the OAuth2 functionality. For Step CA certificate trust, use the step-ca-trust extension.

## 🌐 Networks

- **Development**: `gitea-network` (internal)
- **DevContainer**: `gitea-workspace-network` (external)
- **Let's Encrypt**: `letsencrypt-network` (external)
- **Step CA**: `step-ca-network` (external)

## 🔒 Security

⚠️ **Production Checklist:**

- Change default admin credentials
- Configure firewall rules for SSH (port 2222)
- Use strong admin passwords
- Regular security updates
- Configure proper backup strategy

## 🆘 Troubleshooting

**Build Issues:**

- Ensure `sb` (stackbuilder) is installed: <https://github.com/zyrakq/stackbuilder>
- Check component file syntax
- Verify all required files exist

**Access Issues:**

- Check port forwarding configuration
- Verify network connectivity
- Review container logs: `docker logs gitea`

**SSL Issues:**

- **Let's Encrypt**: Verify domain DNS and letsencrypt-manager
- **Step CA**: Check step-ca-manager and virtual network config

## 📝 Notes

- The `build/` directory is automatically generated and should not be edited manually
- Environment variables in generated files use `$VARIABLE_NAME` format for proper interpolation
- Each generated configuration includes a complete `docker-compose.yml` and `.env.example`
- Gitea data is persisted in the `gitea-data` Docker volume
- SSH access is available on port 2222 for Git operations

## 📚 Additional Resources

- [Gitea Documentation](https://docs.gitea.io/)
- [Gitea Configuration Cheat Sheet](https://docs.gitea.io/en-us/config-cheat-sheet/)
- [Gitea Docker Installation](https://docs.gitea.io/en-us/install-with-docker/)
- [Gitea OAuth2 Configuration](https://docs.gitea.io/en-us/usage/authentication/#oauth2)
- [Stackbuilder Documentation](https://github.com/zyrakq/stackbuilder)
