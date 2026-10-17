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
docker compose pull && docker compose up -d
./update-frontend.sh $FRONTEND_TAG             # pulls the SPA + installs the Caddy site
```

Requires: the central Caddy container joined to the `reverse-proxy` network,
Cloudflare DNS `plushboard.sarto.dev` → VPS (TLS mode "Full" for the host),
and `https://plushboard.sarto.dev/auth/callback` in the GCP OAuth client.

## Data (never in this repo)

The SQLite database lives in the `backend_data` volume — carry it over from
the dev machine with `scp data/plushboard.db vps:/var/lib/docker/volumes/plushboard-infra_backend_data/_data/`
(or any staging path you own). Ledger seeds and exports stay out of git.

## Updating

```bash
docker compose pull && docker compose up -d    # backend: latest image
./update-frontend.sh <new-tag>                 # frontend: new release
```
