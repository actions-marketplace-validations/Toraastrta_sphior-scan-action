# SPHIOR CODE Security Scan

Keep AI-written vulnerabilities out of main.

This action scans your repository on every push and pull request for hard-coded secrets,
insecure code patterns mapped to CWE, and taint flows where untrusted input reaches a query,
a shell, `eval`, HTML, a file path or a URL. It fails the job when a finding reaches the
severity you choose, writes a summary to the job page, and can send the results to the
GitHub Security tab.

- **The same result every time.** Checks follow fixed rules, so the same code always gives
  the same result. No AI and no AI cost.
- **Runs inside your runner.** Your source code is not uploaded anywhere. No account and no
  API key.
- **14 languages.** JavaScript, TypeScript, Python, Go, Java, C#, Ruby, PHP, Rust, Kotlin,
  Swift, Scala, C and C++, plus pattern rules for SQL files.

## Usage

```yaml
# .github/workflows/sphior-code.yml
name: SPHIOR CODE
on: [push, pull_request]

jobs:
  scan:
    runs-on: ubuntu-latest
    permissions:
      contents: read
      security-events: write   # to send results to the Security tab
    steps:
      - uses: actions/checkout@v6
      - uses: Toraastrta/sphior-scan-action@v1
```

That is all. Critical and high findings fail the job, and every finding is listed in the
job summary.

### Block merges

Make the `scan` job a required status check in your branch protection rule or ruleset. A pull
request that adds a critical or high finding cannot be merged until it is fixed or accepted
with a reason.

### Choose the threshold

```yaml
      - uses: Toraastrta/sphior-scan-action@v1
        with:
          fail-on: medium      # critical, high, medium, low, info, or none
```

### Scan one directory

```yaml
      - uses: Toraastrta/sphior-scan-action@v1
        with:
          path: services/api
```

Locations in the results stay relative to the repository root, so alerts in the Security tab
point at the right files.

## Inputs

| Input | Default | What it does |
|---|---|---|
| `path` | `.` | Directory to scan, relative to the repository root. |
| `fail-on` | `high` | Fail the job when a finding is at or above `critical`, `high`, `medium`, `low` or `info`. `none` never fails. |
| `sarif-file` | `sphior-code.sarif` | Where to write the SARIF 2.1.0 report. |
| `upload-sarif` | `auto` | Send the results to GitHub code scanning. `auto` does it for public repositories only. `true` always, `false` never. |
| `version` | `0.1.11` | Version of [`@sphior/scan`](https://www.npmjs.com/package/@sphior/scan) to run. |

A mistyped input stops the job with an error instead of quietly turning the check off.

## Outputs

| Output | What it is |
|---|---|
| `findings` | Total number of findings. |
| `critical`, `high`, `medium` | Findings at each severity. |
| `low` | Low and info findings. |
| `sarif-file` | Path of the SARIF report. |

## Private repositories

Sending results to the Security tab of a private repository needs GitHub Advanced Security,
so `upload-sarif: auto` skips it there. The scan, the job summary and the failing check still
work. If your organisation has Advanced Security, set `upload-sarif: true` and add
`actions: read` to the job permissions.

## Excluding paths and accepting findings

Generated or vendored code can be excluded with a `.sphiorignore` file, and a finding you have
reviewed can be accepted with a written reason, in the file or inline with a `sphior-ignore`
comment. See the
[SPHIOR CODE documentation](https://github.com/Toraastrta/sphior-code#excluding-paths-and-accepting-findings).

## Versions

Pin the action to a major version (`@v1`) or a full commit SHA. The scanner version is pinned
by the `version` input, so a ruleset update never changes your results without a change on
your side.

## More from SPHIOR CODE

The same engine runs in VS Code, Cursor, JetBrains IDEs, the command line and AI coding
assistants over MCP. With the [SPHIOR CODE GitHub App](https://github.com/apps/sphior-code),
every pull request is scanned without a workflow file and findings are annotated on the
changed lines. See [sphior.com/code](https://sphior.com/code).

## License

MIT
