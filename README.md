# SongBird Domains frontend

Frontend for .sgb domain service powered by the [Punk Domains protocol](https://punk.domains).

## Environment variables

- `VITE_APP_API_BASE_URL`: The base URL of the API (for /data-connector page).

## Quickstart

```bash
npm install
npm run dev
```

## tokens.json

Add the correct tokens (for the Send Tokens page).

## Branches & deployment

- **Important:** Never commit directly to the `main` branch.
- Development is done on the `develop` branch (or temporary branches which then merge with the `develop` branch).
- Deployment: When you want to make deployment to the production server, merge `develop` into the `main` branch. A CI/CD system on GitHub (GitHub Actions) will automatically build and deploy the new code to GitHub Pages.