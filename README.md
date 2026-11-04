# Odoo 19 project template

My personal template for running and developing with Odoo 19 locally

Prerequisites: Docker, uv, an SSH key with access to `odoo/enterprise`

## Setup

```bash
git clone --branch 19.0 git@github.com:mrkss02/odoo-project-template.git <project>
```

### 1. Clone Odoo and Odoo enterprise

```bash
cd <project>/odoo
git clone --depth 1 --revision <community-commit> git@github.com:odoo/odoo.git community
git clone --depth 1 --revision <enterprise-commit> git@github.com:odoo/enterprise.git enterprise
```

### 2. Project repos

Clone the project's repos next to `odoo/`:

```bash
git clone <repo-url> ../<repo>
```

### 3. Config

```bash
cp .env.example .env
cp odoo.conf.example odoo.conf
```

Replace every `<placeholder>` in `.env`, `odoo.conf`, `requirements-extra.txt`, `../odools.toml`
(same name = same value):

| Placeholder | Value | Files |
|---|---|---|
| `<project-name>` | e.g. `name` | `.env` |
| `<db-port>` | e.g. `54NN` | `.env`, `odoo.conf` |
| `<http-port>` | e.g. `80NN` | `odoo.conf` |
| `<postgres-image>` | e.g. `postgres:17` or `pgvector/pgvector:pg17-bookworm` | `.env` |
| `<postgres-user>`, `<postgres-password>` | e.g. `odoo`, `odoo` | `.env`, `odoo.conf` |
| `<admin-password>` | e.g. `admin` | `odoo.conf` |
| `<repo>` | e.g. `repo` | `odoo.conf`, `../odools.toml` |
| `<path-to-repo>` | e.g. `../<repo>` | `requirements-extra.txt` |

Project-specific Odoo options (e.g. `server_wide_modules` + `queue_job_*`) go into `odoo.conf` as well.

To check everything is filled, run:

```bash
grep -n '<' .env odoo.conf requirements-extra.txt ../odools.toml
```

### 4. Python

```bash
uv venv --python 3.12 venv
uv pip install --python venv -r community/requirements.txt -r requirements-extra.txt
```

(Or point at a shared venv, then change the Python path in `../.vscode/launch.json` and `../odools.toml`.)

### 5. Run

```bash
docker compose up -d --wait
venv/bin/python community/odoo-bin -c odoo.conf
```

## Database

From an Odoo backup zip:

```bash
venv/bin/python community/odoo-bin db -c odoo.conf load --neutralize <db> <backup.zip>
```

## Updating version

Reclone at the new commits and upgrade all modules:

```bash
rm -rf community enterprise
# do step 1 with new commits
venv/bin/python community/odoo-bin -c odoo.conf -d <db> -u all --stop-after-init
```
