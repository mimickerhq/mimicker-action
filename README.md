# Mimicker Action

GitHub Action to start a [Mimicker](https://github.com/mimickerhq/mimicker) HTTP mock server in your CI workflows.

[![Action tests](https://github.com/mimickerhq/mimicker-action/actions/workflows/test.yml/badge.svg)](https://github.com/mimickerhq/mimicker-action/actions/workflows/test.yml)

---

## Usage

### Minimal — no stubs file (add stubs via API at runtime)

```yaml
steps:
  - uses: mimickerhq/mimicker-action@v1

  - name: Hit your mock
    run: curl http://localhost:8080/__mimicker__/health
```

### With a stubs YAML file

```yaml
steps:
  - uses: actions/checkout@v4

  - uses: mimickerhq/mimicker-action@v1
    with:
      config: stubs/api.yaml

  - name: Run tests against the mock
    run: pytest
```

### Custom port

```yaml
- uses: mimickerhq/mimicker-action@v1
  with:
    port: 9090
```

### Capture the URL as an output

```yaml
- uses: mimickerhq/mimicker-action@v1
  id: mock

- run: curl ${{ steps.mock.outputs.url }}/my-endpoint
```

### Stub coverage report (detect test decay)

After your tests run, call the report script to get a summary of which stubs were hit and which were never exercised. The report is written to the GitHub Actions job summary.

```yaml
- uses: actions/checkout@v4

- uses: mimickerhq/mimicker-action@v1
  id: mock
  with:
    config: stubs/api.yaml

- name: Run tests
  run: pytest

- name: Mimicker coverage report
  if: always()
  run: bash ${{ steps.mock.outputs.report-script }}
```

Add `fail-on-unused: true` to fail the job if any stub was never hit:

```yaml
- uses: mimickerhq/mimicker-action@v1
  id: mock
  with:
    config: stubs/api.yaml
    fail-on-unused: true

- name: Run tests
  run: pytest

- name: Mimicker coverage report
  if: always()
  run: bash ${{ steps.mock.outputs.report-script }}
```

---

## Inputs

| Input | Description | Default |
|---|---|---|
| `port` | Port to run Mimicker on | `8080` |
| `config` | Path to a stubs YAML file (relative to repo root) | — |
| `version` | Mimicker Docker image tag | `latest` |
| `wait-timeout` | Seconds to wait for the server to become healthy | `30` |
| `fail-on-unused` | Fail the job if any stubs were never hit (detects test decay) | `false` |

## Outputs

| Output | Description |
|---|---|
| `url` | Base URL of the running server, e.g. `http://localhost:8080` |
| `report-script` | Path to a shell script that writes the stub coverage report to the job summary. Call it with `if: always()` after your tests. |

---

## Stubs file format

```yaml
# stubs/api.yaml
routes:
  - method: GET
    path: /users
    status: 200
    body:
      - id: 1
        name: Alice

  - method: POST
    path: /orders
    status: 201
    body:
      id: 42
      status: created
```

See the [Mimicker docs](https://mimickerhq.github.io/mimicker/) for the full YAML reference.

---

## License

Apache 2.0 — see [LICENSE](LICENSE).
