# Preflight MCP Scan

A GitHub Action that scans a remote MCP server with [Preflight](https://preflight.allthepossibles.com) and fails the job when a finding reaches the severity you choose. It writes the verdict and a table of findings to the job summary.

## Usage

```yaml
name: MCP scan
on: [push, pull_request]
jobs:
  preflight:
    runs-on: ubuntu-latest
    steps:
      - uses: johnhowrey/preflight-action@v1
        with:
          url: https://example.com/mcp
          fail-on: high
```

To use the results in a later step, give the step an `id`:

```yaml
      - uses: johnhowrey/preflight-action@v1
        id: scan
        with:
          url: https://example.com/mcp
          fail-on: never
      - run: echo "Verdict was ${{ steps.scan.outputs.verdict }}"
```

## Inputs

| Input | Required | Default | Description |
| --- | --- | --- | --- |
| `url` | yes | | Streamable HTTP endpoint of the server to scan. |
| `fail-on` | no | `high` | Lowest severity that fails the job: `low`, `medium`, `high` or `never`. Findings with severity `critical` count as `high`. |
| `api-key` | no | | Preflight Pro key (`pf_...`). Store it as a repository secret. |

## Outputs

| Output | Description |
| --- | --- |
| `verdict` | `clear`, `caution` or `high-risk`. |
| `findings-json` | The `findings` array from the scan, as a JSON string. |

## Limits

The free tier allows 10 scans a day per IP address. GitHub-hosted runners share IP addresses, so a busy repository can hit the limit; a Pro key removes it. Pro keys also enable semantic prompt-injection detection.

The action needs `curl` and `jq`, both of which are installed on GitHub-hosted Linux and macOS runners.

## License

MIT.
