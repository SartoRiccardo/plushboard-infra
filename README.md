# plushboard-infra

Deployment repo for Plushboard. Pairs with
[plushboard-back](https://github.com/SartoRiccardo/plushboard-back) (API
image on ghcr) and
[plushboard-front](https://github.com/SartoRiccardo/plushboard-front)
(static SPA releases).

## VPS setup (first time)

```bash
git clone https://github.com/SartoRiccardo/plushboard-infra && cd plushboard-infra
cp secrets/.env.app.example secrets/.env.app   # then fill it
cp .env.example .env                           # set CADDY_DIR + FRONTEND_TAG
docker compose pull && docker compose up -d    # backend (API) on the reverse-proxy network
./update-frontend.sh $FRONTEND_TAG             # SPA into the central Caddy + site config
```

Requires: the central Caddy container joined to the `reverse-proxy` network,
Cloudflare DNS `plushboard.sarto.dev` → VPS (TLS mode "Full" for the host),
and `https://plushboard.sarto.dev/api/auth/callback` in the GCP OAuth client.

## Data

SQLite lives in the `backend_data` volume. To carry over the dev machine's
history: `scp data/plushboard.db` from the dev checkout into the volume path
(`docker volume inspect plushboard-infra_backend_data`) and restart the
backend. Ledger seeds/exports stay out of this repo.

## Updating

```bash
docker compose pull && docker compose up -d    # backend: latest image
./update-frontend.sh <new-tag>                 # frontend: new release
```
