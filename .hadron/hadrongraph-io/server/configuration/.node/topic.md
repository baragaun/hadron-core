# Configuration

## `.hadron-controller.yaml`

Place this file in the directory pointed to by `CONFIG_PATH` (default: project root).

```yaml
graphs:
  myorg/myrepo:
    source: "git@github.com:myorg/myrepo.git"
    version: "latest"
    path: "myorg/myrepo"          # relative to SOURCES_PATH
    updatedAt: "2026-01-01T00:00:00.000Z"
```

| Key | Description |
|-----|-------------|
| `source` | Git clone URL (SSH or HTTPS) |
| `path` | Local path relative to `SOURCES_PATH` where the repo is cloned |
| `version` | Label only — currently unused |

**Graph name** (the YAML key) also determines the **namespace** stored in the DB:
slashes become dots — `myorg/myrepo` → namespace `myorg.myrepo`.

## Node Location

The controller syncs nodes from the configured repo. Nodes are stored:
- At the **repo root** level, e.g. `myorg/myrepo/.node/` or `myorg/myrepo/module/.node/`
- Inside **`.hadron/`** at the repo root, e.g. `myorg/myrepo/.hadron/myorg/myrepo/.node/`

Both locations are walked. The `.hadron/` prefix is stripped from the `loc`
so that `.hadron/myorg/myrepo/module` → loc `myorg.myrepo.module`.

## Environment Variables

| Variable | Default | Description |
|----------|---------|-------------|
| `DATABASE_URL` | — | Prisma connection string |
| `DB_PROVIDER` | `postgresql` | `postgresql` or `sqlite` |
| `JWT_SECRET` | — | HS256 signing secret (≥ 32 chars) |
| `SOURCES_PATH` | `./sources` | Root directory for cloned graph repos |
| `CONFIG_PATH` | `.` | Directory containing `.hadron-controller.yaml` |
| `PORT` | `4000` | HTTP listen port |
| `GITHUB_CLIENT_ID` | — | GitHub OAuth App client ID |
| `GITHUB_CLIENT_SECRET` | — | GitHub OAuth App client secret |
| `GITHUB_ORG` | — | If set, only org members can sign in |
| `BASE_URL` | — | Server's public URL (for OAuth callback) |
| `FRONTEND_URL` | — | Where to redirect after OAuth |
