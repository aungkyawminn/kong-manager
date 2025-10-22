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

Edit `.env` to configure your Kong Admin API URL:

```env
NODE_ENV=production

# Set the Kong Control Plane API URL
# Examples:
# VITE_ADMIN_API_URL=http://localhost:8001
# VITE_ADMIN_API_URL=https://kong-admin.example.com
# VITE_ADMIN_API_URL=http://192.168.1.100:8001
VITE_ADMIN_API_URL=
```

**Important:** If `VITE_ADMIN_API_URL` is not set, the app will default to `http://<current-host>:8001`

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

### Example Configurations

**Local Kong instance:**
```env
VITE_ADMIN_API_URL=http://localhost:8001
```

**Remote Kong instance:**
```env
VITE_ADMIN_API_URL=https://kong-admin.example.com
```

**Kong in Docker:**
```env
VITE_ADMIN_API_URL=http://192.168.1.100:8001
```

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
