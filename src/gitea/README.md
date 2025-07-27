# 🍵 Gitea Service

A modular Docker Compose configuration system for Gitea with SQLite backend and support for multiple environments.

## 🏗️ Project Structure

```sh
src/gitea/
├── components/                              # Source compose components
│   ├── base/                               # Base components
│   │   ├── docker-compose.yml              # Main Gitea service
│   │   └── .env.example                    # Base environment variables
│   ├── environments/                       # Environment components
│   │   ├── devcontainer/
│   │   │   └── docker-compose.yml          # DevContainer environment
│   │   ├── forwarding/
│   │   │   └── docker-compose.yml          # Development with port forwarding
│   │   ├── letsencrypt/
│   │   │   ├── docker-compose.yml          # Let's Encrypt SSL
│   │   │   └── .env.example                # Let's Encrypt variables
│   │   └── step-ca/
│   │       ├── docker-compose.yml          # Step CA SSL
│   │       └── .env.example                # Step CA variables
│   └── extensions/                         # Extension components
│       └── step-ca-trust/
│           ├── docker-compose.yml          # Step CA trust configuration
│           └── .env.example                # Step CA trust variables
├── build/                        # Generated configurations (auto-generated)
│   ├── devcontainer/
│   │   ├── base/                 # DevContainer + base
│   │   └── step-ca-trust/        # DevContainer + base + step-ca-trust
│   ├── forwarding/
│   │   ├── base/                 # Development + base
│   │   └── step-ca-trust/        # Development + base + step-ca-trust
│   ├── letsencrypt/
│   │   ├── base/                 # Let's Encrypt + base
│   │   └── step-ca-trust/        # Let's Encrypt + base + step-ca-trust
│   └── step-ca/
│       ├── base/                 # Step CA + base
│       └── step-ca-trust/        # Step CA + base + step-ca-trust
├── build.sh                      # Build script
└── README.md                     # This file
```

## 🚀 Quick Start

### 1. Build Configurations

Run the build script to generate all possible combinations:

```bash
./build.sh
```

This will create all combinations in the `build/` directory.

### 2. Choose Your Configuration

Navigate to the desired configuration directory:

```bash
# For development with port forwarding
cd build/forwarding/base/

# For DevContainer environment
cd build/devcontainer/base/

# For production with Let's Encrypt SSL
cd build/letsencrypt/base/

# For production with Step CA SSL
cd build/step-ca/base/
```

### 3. Configure Environment

Copy and edit the environment file:

```bash
cp .env.example .env
# Edit .env with your values
```

### 4. Deploy

Start the services:

```bash
docker-compose up -d
```

Access: `http://localhost:3000` (for forwarding mode)

## 🔧 Available Configurations

### Environments

- **devcontainer**: Development environment with workspace network
- **forwarding**: Development environment with port forwarding (3000, 2222)
- **letsencrypt**: Production with Let's Encrypt SSL certificates
- **step-ca**: Production with Step CA SSL certificates

### Generated Combinations

Each environment provides a base configuration:

- `devcontainer/base` - DevContainer development setup
- `forwarding/base` - Development with port forwarding
- `letsencrypt/base` - Production with Let's Encrypt SSL
- `step-ca/base` - Production with Step CA SSL

**Extensions available:** step-ca-trust (for OIDC certificate trust)

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

## 🛠️ Development

### Adding New Environments

1. Create directory in `components/environments/` with `docker-compose.yml` and optional `.env.example` file
2. Run `./build.sh` to generate new combinations

### File Naming Convention

All component files follow the standard Docker Compose naming convention (`docker-compose.yml`) for:

- **VS Code compatibility**: Full support for Docker Compose language features and IntelliSense
- **IDE integration**: Proper syntax highlighting and validation in all major editors
- **Tool compatibility**: Works with Docker Compose plugins and extensions
- **Standard compliance**: Follows official Docker Compose file naming patterns

### Modifying Existing Components

1. Edit the component files in `components/`
2. Run `./build.sh` to regenerate configurations
3. The `build/` directory will be completely recreated

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

- Ensure `yq` is installed: <https://github.com/mikefarah/yq#install>
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
- Missing `.env.*` files for components are handled gracefully by the build script
- Gitea data is persisted in the `gitea-data` Docker volume
- SSH access is available on port 2222 for Git operations

## 🔄 Migration from Legacy Deploy Script

The new build system provides a modular approach to configuration management:

**New approach:**

```bash
./build.sh
cd build/forwarding/base/
cp .env.example .env
docker-compose up -d
```

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

## 📚 Additional Resources

- [Gitea Documentation](https://docs.gitea.io/)
- [Gitea Configuration Cheat Sheet](https://docs.gitea.io/en-us/config-cheat-sheet/)
- [Gitea Docker Installation](https://docs.gitea.io/en-us/install-with-docker/)
- [Gitea OAuth2 Configuration](https://docs.gitea.io/en-us/usage/authentication/#oauth2)
