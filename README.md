# ci-workflows

Reusable GitHub Actions building blocks shared by 0verme projects.

## Cloudflare Worker Deploy (composite action)

Deployment primitive for Cloudflare Workers: Cloudflare auth + `wrangler deploy`,
running inside the **calling job** so build outputs stay visible without an
artifact round-trip. The caller owns checkout, Node setup, install and build;
this action only owns the deploy step.

### Usage

```yaml
- name: Deploy Worker
  uses: 0verme/ci-workflows/.github/actions/cloudflare-worker-deploy@v1
  with:
    working_directory: .            # wrangler project dir, relative to repo root
    command: deploy                 # only "deploy" is supported
    api_token: ${{ secrets.CLOUDFLARE_API_TOKEN }}
    account_id: ${{ secrets.CLOUDFLARE_ACCOUNT_ID }}
```

### Inputs

| input              | required | default  | description                                        |
| ------------------ | -------- | -------- | -------------------------------------------------- |
| `working_directory`| no       | `.`      | Wrangler project directory relative to repo root   |
| `command`          | no       | `deploy` | Wrangler command; any value other than `deploy` is rejected |
| `wrangler_version` | no       | (action default) | Pin a Wrangler version when needed       |
| `api_token`        | yes      | -        | Cloudflare API token (`CLOUDFLARE_API_TOKEN` secret) |
| `account_id`       | yes      | -        | Cloudflare account ID (`CLOUDFLARE_ACCOUNT_ID` secret) |

### Required secrets

- `CLOUDFLARE_API_TOKEN`
- `CLOUDFLARE_ACCOUNT_ID`

Secret values never appear in this repository.

## Cloudflare Pages Deploy (composite action)

Deployment primitive for Cloudflare Pages Direct Upload: Cloudflare auth +
`wrangler pages deploy`, running inside the **calling job** so static build
output stays visible without an artifact round-trip. The caller owns checkout,
Node setup, install and build; this action only owns the deploy step and never
creates or reconfigures Pages projects, domains or DNS.

> Available since **v1.1.0**. The `v1` major tag points to v1.1.0; pins that
> predate the v1.1.0 release will not contain this action.

### Usage

```yaml
- name: Deploy Pages
  uses: 0verme/ci-workflows/.github/actions/cloudflare-pages-deploy@v1
  with:
    directory: static            # static build output, relative to working_directory
    project_name: jinggua        # existing Cloudflare Pages project
    branch: main                 # production branch; pass github.ref_name for previews
    api_token: ${{ secrets.CLOUDFLARE_API_TOKEN }}
    account_id: ${{ secrets.CLOUDFLARE_ACCOUNT_ID }}
```

### Inputs

| input              | required | default  | description                                        |
| ------------------ | -------- | -------- | -------------------------------------------------- |
| `directory`        | yes      | -        | Directory to deploy, relative to `working_directory` |
| `project_name`     | yes      | -        | Existing Cloudflare Pages project name             |
| `branch`           | no       | (empty)  | Deployment branch; required in CI, where the checkout is a detached HEAD and Wrangler cannot detect the branch from git |
| `working_directory`| no       | `.`      | Wrangler project directory relative to repo root   |
| `wrangler_version` | no       | (action default) | Pin a Wrangler version when needed       |
| `api_token`        | yes      | -        | Cloudflare API token (`CLOUDFLARE_API_TOKEN` secret) |
| `account_id`       | yes      | -        | Cloudflare account ID (`CLOUDFLARE_ACCOUNT_ID` secret) |

### Required secrets

- `CLOUDFLARE_API_TOKEN`
- `CLOUDFLARE_ACCOUNT_ID`

Secret values never appear in this repository.