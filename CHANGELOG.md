# Changelog — `armature-mail`

All notable changes to this crate will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.0.0/),
and this crate adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

Earlier changes are recorded in the workspace [`CHANGELOG.md`](../CHANGELOG.md).

## [Unreleased]

### Security

- **Breaking:** `Email::header` (and `validate_header`) refuse structural headers: `To`, `Cc`, `Bcc`, `From`, `Sender`, `Reply-To`, `Return-Path`, `Subject`, `Date`, `Message-ID`, `In-Reply-To`, `References`, `MIME-Version` and every `Content-*` header, matched case-insensitively. A custom header could otherwise silently add recipients or spoof the sender. Such a header is reported by `Email::validate` and never reaches the wire. Migration: use the typed builder methods (`bcc`, `reply_to`, `subject`, ...).
- **Breaking:** the dead-letter queue is bounded in age and length. New `EmailQueueConfig::dead_letter_ttl` (default 7 days, `DEFAULT_DEAD_LETTER_TTL`) and `dead_letter_max_len` (default 1 000, `DEFAULT_DEAD_LETTER_MAX_LEN`); dead-lettered jobs carry full emails (reset/login links), which were previously kept forever. `RedisBackend` trims the `:dead` list (`LTRIM`) and refreshes its TTL (`PEXPIRE`) on every push; `InMemoryBackend` expires each entry individually (new `InMemoryBackend::dead_letter_retention`) and caps its DLQ at `dead_letter_max_len` instead of `max_queue_depth`. A zero TTL is rejected by `EmailQueueConfig::validate`. Migration: struct literals of `EmailQueueConfig` need the two new fields (or `..Default::default()`).

## [0.2.2] - 2026-09-15

### Changed

- The `ses` feature's AWS SDK dependencies bumped: `aws-sdk-sesv2` 1.112→1.133 (one release short of the newest, for the same `aws-smithy-types` 1.7 break) and `aws-config` 1.8→1.12.
- A direct `aws-smithy-types >=1.6.3, <1.7` requirement keeps a fresh resolve on the SDK releases held back above; without it the resolver picks `aws-sdk-*`/`aws-runtime` releases that need `aws-smithy-types` 1.7 and fail to build against `aws-config` 1.12.
- AWS SDK dependencies no longer enable their default features, dropping the SDK's legacy hyper-0.14 client and its `h2 0.3` (RUSTSEC-2026-0258); the hyper-1 `default-https-client` and `rt-tokio` (plus `sigv4a`/`http-1x` where the SDK enabled them by default) are kept.
- The MSRV CI job also checks `--all-features`, so the optional AWS SDK dependencies are built on the MSRV toolchain.

