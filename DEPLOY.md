# Deployment Runbook — hbtn-devops-pipeline-lab

## Trigger

A push to `main` that passes the `test` job automatically triggers `build` (Docker image to GHCR) then `deploy` (Render staging).

## Target

Render Web Service connected to a Render PostgreSQL database (internal connection string).

## Database configuration

`DATABASE_URL` is set as an environment variable on the Render Web Service, pointing to the internal Render PostgreSQL connection string. It is never written in the workflow file or repository.

## Verification

After a successful deploy job, verify both endpoints:

```bash
curl -s https://<your-render-service>.onrender.com/health
curl -s https://<your-render-service>.onrender.com/items
```

Both must return HTTP 200. `/health` confirms process liveness; `/items` confirms the application can query PostgreSQL.

## Rollback

To roll back to a specific commit image:

```bash
# Pull the immutable commit-tagged image
docker pull ghcr.io/souf-f/hbtn-devops-pipeline-lab:<commit-sha>

# Update the Render service to use that image tag via the Render dashboard
# or trigger a deploy with the explicit image reference.
```

## Credential cleanup

1. Revoke `RENDER_API_KEY` in the Render dashboard under Account Settings → API Keys.
2. Remove the `RENDER_API_KEY` and `RENDER_SERVICE_ID` secrets from the GitHub repository Settings → Secrets.
3. `GITHUB_TOKEN` is automatic and ephemeral — no cleanup needed.
4. If the GHCR package was made public, set it back to private or delete it under GitHub → Packages.
