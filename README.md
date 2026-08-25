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