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

---

## Inputs

| Input | Description | Default |
|---|---|---|
| `port` | Port to run Mimicker on | `8080` |
| `config` | Path to a stubs YAML file (relative to repo root) | — |
| `version` | Mimicker Docker image tag | `latest` |
| `wait-timeout` | Seconds to wait for the server to become healthy | `30` |

## Outputs

| Output | Description |
|---|---|
| `url` | Base URL of the running server, e.g. `http://localhost:8080` |

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
