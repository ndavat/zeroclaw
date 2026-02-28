# Deploying ZeroClaw to Render.com

ZeroClaw is designed to be extremely lightweight, making it a perfect fit for the **Render.com Free Tier**.

## 1. Quick Start (Blueprint)

This repository includes a `render.yaml` file that allows for a "one-click" deployment using Render Blueprints.

1.  Push this branch (`render-groq-free`) to your GitHub repository.
2.  Log in to [Render.com](https://render.com).
3.  Click **New +** and select **Blueprint**.
4.  Connect your repository.
5.  Render will detect the `render.yaml` and configure:
    -   **Service Type**: Web Service (Docker)
    -   **Plan**: Free
    -   **Runtime**: Docker (using the `release` stage for minimal size)
    -   **Port**: 10000 (Mapped automatically via `ZEROCLAW_GATEWAY_PORT`)
6.  **Environment Variables**:
    -   You **MUST** set `GROQ_API_KEY` in the Render dashboard after the initial sync.

## 2. Persistence (Important)

Render's Free Tier web services **do not have persistent storage**. This means:
-   Any configuration changes made via the Web Dashboard will be lost on restart.
-   Your pairing tokens and memory (SQLite) will be reset.

### Options for Persistence:
1.  **Stateless Mode (Default)**: Keep your configuration in environment variables in `render.yaml`. Every restart starts fresh.
2.  **External Postgres**: Render offers a free Postgres database (valid for 90 days). You can connect it by setting:
    -   `ZEROCLAW_STORAGE_PROVIDER`: `postgres`
    -   `ZEROCLAW_STORAGE_DB_URL`: Your Render Postgres connection string.

## 3. Keeping the Service Awake

Render Free Tier services spin down after 15 minutes of inactivity. To keep your bot always responsive:
-   Pulse it every 10-14 minutes using an external monitor like [UptimeRobot](https://uptimerobot.com) targeting your `https://your-app.onrender.com/health` endpoint.

## 4. Groq Free Tier

We've configured `llama-3.3-70b-versatile` on Groq. It provides:
-   **Ultra-fast inference** (500+ tokens/sec).
-   **Generous rate limits** for a free tier.
-   **Unlimited** technically means no monthly cap, but subject to per-minute/per-day rate limits.
