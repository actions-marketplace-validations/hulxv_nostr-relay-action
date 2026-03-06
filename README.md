# nostr-relay-action

A GitHub Action that downloads and starts a pre-built [`nostr-rs-relay`](https://github.com/scsibug/nostr-rs-relay) for use in CI/CD workflows. no compilation required.

## Usage

```yaml
- uses: hulxv/nostr-relay-action@master
  with:
    version: "0.9.0"
    port: "8080"
```

## Supported Platforms

| OS    | Architecture | Artifact                       |
| ----- | ------------ | ------------------------------ |
| Linux | x86_64       | `nostr-rs-relay-linux-x86_64`  |
| Linux | aarch64      | `nostr-rs-relay-linux-aarch64` |
| macOS | x86_64       | `nostr-rs-relay-macos-x86_64`  |
| macOS | arm64 (M1+)  | `nostr-rs-relay-macos-aarch64` |

> The correct binary is automatically detected based on the runner's OS and architecture.

## Inputs

### Core

| Input     | Description                                           | Required | Default   |
| --------- | ----------------------------------------------------- | -------- | --------- |
| `version` | Release tag from this repo (e.g. `0.9.0` or `master`) | Yes      | -         |
| `port`    | Port to run the relay on                              | No       | `8000`    |
| `address` | Address to bind the relay on                          | No       | `0.0.0.0` |
| `start`   | Whether to start the relay after downloading          | No       | `true`    |

### Info

| Input         | Description                    | Required | Default               |
| ------------- | ------------------------------ | -------- | --------------------- |
| `relay_url`   | Public-facing URL of the relay | No       | -                     |
| `name`        | Relay name                     | No       | `nostr-rs-relay (CI)` |
| `description` | Relay description              | No       | -                     |
| `pubkey`      | Relay operator pubkey (hex)    | No       | -                     |
| `contact`     | Relay operator contact         | No       | -                     |

### Limits

| Input                   | Description                                              | Required | Default  |
| ----------------------- | -------------------------------------------------------- | -------- | -------- |
| `max_event_bytes`       | Maximum size of an EVENT message in bytes                | No       | `262144` |
| `max_ws_message_bytes`  | Maximum WebSocket message size in bytes                  | No       | `262144` |
| `max_ws_frame_bytes`    | Maximum WebSocket frame size in bytes                    | No       | `262144` |
| `messages_per_sec`      | Max event writes per second (leave empty for unlimited)  | No       | -        |
| `subscriptions_per_min` | Max subscriptions per minute (leave empty for unlimited) | No       | -        |
| `event_kind_blacklist`  | Comma-separated list of event kinds to blacklist         | No       | -        |
| `event_kind_allowlist`  | Comma-separated list of event kinds to allowlist         | No       | -        |
| `limit_scrapers`        | Whether to limit scrapers                                | No       | `false`  |

### Authorization

| Input              | Description                                                     | Required | Default |
| ------------------ | --------------------------------------------------------------- | -------- | ------- |
| `pubkey_whitelist` | Comma-separated list of pubkeys allowed to publish              | No       | -       |
| `nip42_auth`       | Enable NIP-42 authentication                                    | No       | `false` |
| `nip42_dms`        | Send DMs only to authenticated recipients (requires nip42_auth) | No       | `false` |

### Database

| Input          | Description                                | Required | Default  |
| -------------- | ------------------------------------------ | -------- | -------- |
| `db_engine`    | Database engine (`sqlite` or `rocksdb`)    | No       | `sqlite` |
| `db_min_conn`  | Minimum database connections               | No       | `4`      |
| `db_max_conn`  | Maximum database connections               | No       | `8`      |
| `db_in_memory` | Use in-memory database (data lost on stop) | No       | `true`   |

### Options

| Input                   | Description                                                               | Required | Default |
| ----------------------- | ------------------------------------------------------------------------- | -------- | ------- |
| `reject_future_seconds` | Reject events timestamped more than X seconds in the future (empty = off) | No       | -       |

## Examples

### Minimal. just start the relay

```yaml
steps:
  - uses: hulxv/nostr-relay-action@master
    with:
      version: "0.9.0"

  - name: Run tests
    run: cargo test
```

### Custom port and NIP-42 auth

```yaml
steps:
  - uses: hulxv/nostr-relay-action@master
    with:
      version: "0.9.0"
      port: "7777"
      nip42_auth: "true"
      nip42_dms: "true"
```

### Allowlist specific event kinds

```yaml
steps:
  - uses: hulxv/nostr-relay-action@master
    with:
      version: "0.9.0"
      event_kind_allowlist: "1,3,7"
```

### Download only, start manually

```yaml
steps:
  - uses: hulxv/nostr-relay-action@master
    with:
      version: "0.9.0"
      start: "false"

  - name: Start relay manually
    run: ~/.nostr-relay-bin/nostr-rs-relay --config my-config.toml &
```

## Building Releases

Releases are built via the [Build and Release](.github/workflows/release-nostr-relay.yml) workflow. Trigger it manually with a `relay_tag` input pointing to any `nostr-rs-relay` git tag or commit (e.g. `0.9.0` or `master`). Binaries for all supported platforms will be compiled and attached to a GitHub Release tagged `relay-<relay_tag>`.

## License

This project is licensed under the [MIT License](./LICENSE) © 2026 [Mohamed Emad](https://github.com/hulxv).

> **Note:** The pre-built binaries distributed via releases are built from [`nostr-rs-relay`](https://github.com/scsibug/nostr-rs-relay), which is licensed under the [MIT License](https://github.com/scsibug/nostr-rs-relay/blob/master/LICENSE).
