# Changelog — `armature-mail`

All notable changes to this crate will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.0.0/),
and this crate adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

Earlier changes are recorded in the workspace [`CHANGELOG.md`](../CHANGELOG.md).

## [Unreleased]

### Changed

- The `ses` feature's AWS SDK dependencies bumped: `aws-sdk-sesv2` 1.112→1.133 (one release short of the newest, for the same `aws-smithy-types` 1.7 break) and `aws-config` 1.8→1.12.
- A direct `aws-smithy-types >=1.6.3, <1.7` requirement keeps a fresh resolve on the SDK releases held back above; without it the resolver picks `aws-sdk-*`/`aws-runtime` releases that need `aws-smithy-types` 1.7 and fail to build against `aws-config` 1.12.
- AWS SDK dependencies no longer enable their default features, dropping the SDK's legacy hyper-0.14 client and its `h2 0.3` (RUSTSEC-2026-0258); the hyper-1 `default-https-client` and `rt-tokio` (plus `sigv4a`/`http-1x` where the SDK enabled them by default) are kept.
- The MSRV CI job also checks `--all-features`, so the optional AWS SDK dependencies are built on the MSRV toolchain.
