# ringline-quic-proto

A fork of [quinn-proto](https://github.com/quinn-rs/quinn), the QUIC state
machine from the quinn project, maintained for
[ringline](https://github.com/ringline-rs/ringline). The library is named
`ringline_quic_proto`; its API is quinn-proto's, built on
[ringline-tls](https://crates.io/crates/ringline-tls) in place of rustls.

Upstream: quinn-proto-0.11.19

## Why

ringline needs changes to the QUIC packet path, and a QUIC stack built on
the same TLS fork as its TCP connections. This fork carries those changes
and releases them on its own schedule. The design is in ringline's
[docs/rustls-quinn-forks-design.md](https://github.com/ringline-rs/ringline/blob/main/docs/rustls-quinn-forks-design.md).
Until the first QUIC change, a release is the upstream quinn-proto release
named on the `Upstream:` line, renamed and built on ringline-tls, without
the `platform-verifier` feature.

## Branches and versions

- `ringline/0.11` is the default branch. It starts at quinn's
  `quinn-proto-0.11.19` tag. Each later commit is a ringline change, a merge
  of an upstream quinn-proto 0.11.x release, or a release commit, so
  `git diff <newest merged upstream tag> ringline/0.11` is the whole of the
  fork's changes.
- `ringline-quic-proto` 0.1.x is based on quinn-proto 0.11.x. A 0.1.x
  release never changes the API incompatibly. An incompatible API change, a
  `ringline-tls` minor release, or a move to quinn-proto 0.12 is a new minor
  version (0.2.0, ...). An upstream 0.11.x release, which can add API, is a
  patch version.
- The `Upstream:` line above names the upstream tag this version contains.
  Each release's notes carry the same line.
- Upstream releases arrive as pull requests from `admin/ringline-update`.
  Merge them with a merge commit, not a squash. A squash leaves the upstream
  tag out of the branch's history, so the next merge uses the previous
  upstream tag as its base and conflicts on everything upstream changed
  since.
- A release is published by pushing a `vX.Y.Z` tag
  (`ringline-release.yml`). When ringline's main requires an earlier 0.1.x
  version, the same workflow then opens a pull request against it that
  raises the `ringline-quic-proto` requirement to the release and names the
  upstream advisories the raise fixes (`admin/ringline-raise`).
- Only `ringline-quic-proto` is published from this repository; `quinn`,
  `bench`, `perf` and `fuzz` build here against it.

## Using it

Depend on it under its own name:

```toml
[dependencies]
ringline-quic-proto = "0.1"
```

and use `ringline_quic_proto::` paths, or rename the dependency to keep
`quinn_proto::` paths:

```toml
[dependencies]
quinn-proto = { package = "ringline-quic-proto", version = "0.1" }
```

Its rustls types are ringline-tls's: a config built with upstream rustls, or
with a crate that implements upstream rustls's traits such as
`rustls-platform-verifier`, does not work with it.

## Security

Fixes from upstream quinn-proto are merged and released as patch versions.
Report a vulnerability in upstream quinn to the quinn project; report one in
the fork's own changes to the ringline maintainers through GitHub's private
vulnerability reporting on this repository.

## License

MIT or Apache-2.0, as quinn. See the `LICENSE-*` files.
