# Workspace environment

The existing `environment.json` uses the canonical commands in the repository's
root README and needs no changes. It installs dependencies with `npm ci`, runs
`npm run dev:api`, and exposes `npm run dev:web` on port 5173. Vite binds to `::`,
uses a strict port, accepts Aether preview hostnames, and proxies `/api` to the
local API on port 4000.

No secrets, per-boot setup, Docker images, or authenticated-preview hooks are
needed for this local demo.

## Bootstrap verification (2026-10-16)

- Ran the declared `npm ci` setup through `bash -e -o pipefail`: exit 0.
- No `setup_on_boot` command is declared.
- Restarted both declared commands as managed background services after setup.
  API logs confirmed port 4000; Vite logs confirmed port 5173. Both remain running.
- Probed from inside the workspace with `curl --fail --show-error --silent`:
  - `http://127.0.0.1:5173/`: HTTP 200, storefront HTML.
  - `http://127.0.0.1:4000/api/health`: HTTP 200, `{"ok":true}`.
  - `http://127.0.0.1:5173/api/health`: HTTP 200, `{"ok":true}`.
  - `http://127.0.0.1:5173/api/products`: HTTP 200, three products.

The existing configuration is retained unchanged because end-to-end validation
exposed no setup, service, bind-address, or preview-port problem.

`npm ci` reported nine dependency vulnerabilities (four moderate, four high,
one critical). Dependency upgrades are outside this environment bootstrap.
