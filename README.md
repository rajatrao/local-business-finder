# Local Business Finder

Local business finder is a way to find local city based trending stores and products. App helps small business to highlight their store online and for local customers to find trending stores and products.
Customers can up/down vote a store and leave reviews for the product, this way platform dynamically ranks the stores and showcases trending products.

## Screenshots
> Users can search, up/down vote online store based on their experience and also find trending stores in their neighborhood

<img width="1235" height="870" alt="Screenshot 2026-09-28 at 11 13 19 AM" src="https://github.com/user-attachments/assets/6ca0591e-5050-4219-9c84-aac6302b9c50" />

> Users can write reviews of the products and ask directly reach out to store owners via chat(Ask listing) or call

<img width="1186" height="814" alt="Screenshot 2026-09-28 at 11 13 36 AM" src="https://github.com/user-attachments/assets/8b328464-8b6f-4c4a-9502-b81b1c0a728e" />

> Store owners can add and manage their online store

<img width="1211" height="890" alt="Screenshot 2026-09-28 at 11 17 11 AM" src="https://github.com/user-attachments/assets/3fcbd02f-0d38-4b00-baa2-a63505832955" />


## Architecture
<img width="673" height="271" alt="Screenshot 2026-09-21 at 2 17 36 PM" src="https://github.com/user-attachments/assets/d6cab0a9-5a51-45cf-9609-181b3d477ac2" />

Backend is implemented in Golang, the structure is based out of [clean architecture](https://medium.easyread.co/golang-clean-archithecture-efd6d7c43047) supported by UberFx lib

### Stack

- `web/` — Vite + React + TypeScript + Apollo Client
- `api/` — Go, gqlgen, chi, Uber Fx, pgx, clean architecture


## Run locally

```bash
cp .env.example .env
make up          # Postgres on :5432
make seed        # migrate + Austin catalog + demo users
make api         # GraphQL at http://localhost:8080/graphql  (playground /playground)
make web         # http://localhost:5173
```

First web run: `cd web && npm install`.

### Demo accounts

| Email | Password | Role |
| --- | --- | --- |
| `customer@demo.local` | `demo1234` | neighbor |
| `owner@demo.local` | `demo1234` | owner (seeded Austin shops) |

City seeded: **Austin**. Categories: food, coffee, retail, services, health, nightlife, outdoor, home.

## Tests / build

```bash
cd api && go test ./... && go build -o /tmp/ld-api ./cmd/api
cd web && npm install && npx tsc --noEmit && npm run build
```

GraphQL smoke (after api is up):

```bash
curl -s http://localhost:8080/graphql -H 'content-type: application/json' \
  -d '{"query":"{ home(city:\"Austin\", window: ALL) { city rankedStores { rank store { name category upvoteCount } } trendingProducts { product { name displayPrice } } trendingReviews { review { body authorFirstName } } } }"}'
```

## Layout

- `api/cmd/api` — `fx.New` only here
- `api/cmd/seed` — migrate + seed (`-migrate-only` for migrations alone)
- `api/internal/{domain,ports,usecase}`
- `api/internal/adapter/{graphql,postgres,http,oidc,blob,jwt,openai}`
- `api/internal/fxmod` — config, postgres, oidc, blob, usecase, graphql, http
- `api/migrations` — portable SQL, applied on API start and by seed

## Future work
- live streaming of stores by small business owners and engage with users
- ability to buy products from store
- add support for ads
- sponsored search 
