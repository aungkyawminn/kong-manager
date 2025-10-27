# Kong Manager OSS (Custom Fork)

> **Note:** This is a forked version of the [official Kong Manager OSS](https://github.com/Kong/kong-manager) with custom modifications.

[Getting Started](#getting-started) | [Configuration](#configuration) | [Building](#building) | [Original Repository](https://github.com/Kong/kong-manager)

![Kong Manager OSS - Plugin list](./media/Plugin%20list.png)

Kong Manager OSS, a **free** and **open-source** UI for [Kong](https://github.com/kong/kong), the world's most used open source API Gateway.

## What's Different in This Fork?

This fork adds the ability to **configure the Kong Admin API URL via environment variables**, making it easier to:

- Connect to remote Kong instances during development
- Deploy as a standalone SPA to cloud services (AWS S3, Netlify, etc.)
- Point to different Kong environments without rebuilding
- Support custom Kong deployment configurations

Built and maintained with ❤️ by the team at Kong. Fork maintained for custom deployment scenarios.

## Features

Kong Manager OSS allows you to view and edit all Kong Gateway objects using the admin API. It interacts directly with the Kong admin API and does not require a separate database.

![Kong Manager OSS - Service edit](./media/Service%20edit.png)

> **Important:** Kong Manager OSS is only compatible in Kong Gateway 3.4+

Kong Manager OSS is bundled with Kong Gateway, see [Getting Started](#getting-started) for information on how to use it. To run Kong Manager OSS locally, please see the [contributing](#contributing) section.

## Prerequisites

You'll need:
- **Node.js 18+** and **pnpm** installed
- A running **Kong Gateway 3.4+** instance (local or remote)

## Getting Started

### 1. Install Dependencies

```bash
pnpm install
```

### 2. Configure Environment

Create a `.env` file in the root directory:

```bash
cp .env.example .env
```

Edit `.env` to configure your Kong Admin API URL and optional API key:

```env
NODE_ENV=production

# Set the Kong Control Plane API URL
# Examples:
# VITE_ADMIN_API_URL=http://localhost:8001
# VITE_ADMIN_API_URL=https://kong-admin.example.com
# VITE_ADMIN_API_URL=http://192.168.1.100:8001
VITE_ADMIN_API_URL=

# Set the X-API-Key header value for authentication
# Required if your Kong Admin API is behind an ALB/API Gateway with API key auth
# You can generate a secure key using: openssl rand -base64 48 | tr -d '\n'
VITE_API_KEY=
```

**Important:** 
- If `VITE_ADMIN_API_URL` is not set, the app will default to `http://<current-host>:8001`
- If `VITE_API_KEY` is set, it will be sent as an `X-API-Key` header in all API requests

### 3. Start Development Server

```bash
pnpm serve
```

Kong Manager will be available at **http://localhost:8080**

### Setting Up Kong Gateway

If you don't have Kong Gateway running, use the quickstart script:

```bash
curl -Ls https://get.konghq.com/quickstart | bash -s -- -i kong -t latest
```

Or configure an existing Kong instance to allow remote access:

> If running Kong on a remote server, ensure `admin_listen` is binding to `0.0.0.0` rather than `127.0.0.1` in `kong.conf`

## Why do I need this?

You've been using the admin API just fine for years. Why would you want to use a UI?

Kong Manager OSS is a great way to see your Kong Gateway configuration at glance. You can see the routes and plugins configured on a service and drill in to the configuration of each in a single click.

In addition, the plugin configuration UI provides instructions for each configuration option. You can configure a plugin using the UI with helpful tooltips before running `deck dump` to see the final configuration values.

![Kong Manager OSS - Plugin configuration tooltip](./media/Plugin%20configuration%20tooltip.png)

## Configuration

### Environment Variables

This fork supports the following environment variables:

| Variable | Description | Default |
|----------|-------------|----------|
| `VITE_ADMIN_API_URL` | Kong Admin API URL (e.g., `http://localhost:8001` or `https://kong-admin.example.com`) | Uses current hostname with port 8001 |
| `VITE_API_KEY` | API key sent as `X-API-Key` header for authentication (optional) | None |

### Example Configurations

**Local Kong instance (no authentication):**
```env
VITE_ADMIN_API_URL=http://localhost:8001
```

**Remote Kong instance with API key authentication:**
```env
VITE_ADMIN_API_URL=https://kong-admin.example.com
VITE_API_KEY=your-secure-api-key-here
```

### Security Notes

**Generating a Secure API Key:**
```bash
# Generate a secure random API key
openssl rand -base64 48 | tr -d '\n'
```

**Important Security Considerations:**
- Never commit your `.env` file to version control
- The `.env` file is already in `.gitignore` for your protection
- Use different API keys for development, staging, and production environments
- Rotate API keys regularly
- For production deployments to S3/CDN, configure API keys as environment variables in your CI/CD pipeline

**Common Use Cases:**
- **AWS ALB/API Gateway:** Use `X-API-Key` header validation rules
- **Nginx Reverse Proxy:** Configure `proxy_set_header` to validate the key
- **Kong Gateway Plugin:** Use the `key-auth` plugin on your Admin API

## Building

### Build for Kong Gateway (Bundled)

Builds with `/__km_base__/` base path for bundling with Kong Gateway:

```bash
pnpm build
```

Output: `dist/` directory

### Build for SPA Deployment (S3, Netlify, etc.)

Builds with `/` root path for standalone deployment:

```bash
pnpm build:spa
```

Output: `dist/` directory ready for deployment to:
- AWS S3 + CloudFront
- Netlify
- Vercel
- Any static hosting service

#### Deployment Notes

**For S3/CloudFront:**
1. Build with `pnpm build:spa`
2. Upload `dist/` contents to S3 bucket
3. Configure CloudFront or S3 to serve `index.html` for all routes (SPA routing)
4. Set appropriate CORS headers for Kong Admin API

**For SPA routing:**
Ensure your hosting platform redirects all routes to `index.html`. Example for S3:
- Set error document to `index.html` with 200 status code

**CORS Configuration:**
Your Kong Admin API must allow CORS from your deployment domain. Add to `kong.conf`:
```
admin_gui_url = https://your-manager-domain.com
```

### Preview Build Locally

```bash
pnpm preview
```

Or use any static server:
```bash
cd dist
python3 -m http.server 8080
# or
php -S localhost:8080
```

## Contributing

This fork maintains compatibility with the upstream Kong Manager OSS. For general contributions, please refer to the [original repository](https://github.com/Kong/kong-manager).

For fork-specific modifications:
1. Fork this repository
2. Create a feature branch
3. Test your changes with both `pnpm build` and `pnpm build:spa`
4. Submit a pull request

### Technology Stack

- **Framework:** Vue 3
- **Build Tool:** Vite
- **Package Manager:** pnpm
- **Language:** TypeScript
- **UI Components:** Kong's Kongponents library
