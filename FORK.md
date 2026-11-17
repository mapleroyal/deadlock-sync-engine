# Deadlock Sync engine fork

This is a small, independently maintained MIT-licensed fork of
[Deadlock API Ingest v0.4.8](https://github.com/deadlock-api/deadlock-api-ingest/tree/v0.4.8),
upstream commit `ef1882306163826809cb2768a39b4c4f8b258545`.
It supports the [Deadlock Sync supervisor](https://github.com/mapleroyal/deadlock-sync).
It is not an official upstream release.

Version `0.4.8-sync.1` adds `--own-matches --account-id ID32`, where ID32 is a
nonzero 32-bit Steam account ID. The engine selects only that remembered public
Steam account before decrypting its session. An absent or unusable selected
account fails with the existing authentication-error marker; another account is
never substituted. Without `--account-id`, upstream all-account behavior remains.
The supervisor owns current-account priority, per-account retries and cooldowns.

This patch does not change metadata request spacing, upstream attempt records,
quota rules, history order, authentication protections, memory permissions or
live reading. Steam sessions remain local and are used only to authenticate with
Steam. Do not run parallel workers or clear stored attempts to increase throughput.

The workflow is manual and builds/tests Linux and Windows workers without
publishing automatically. Tests use synthetic inputs and do not query real Steam
accounts. GitHub release assets are explicitly published and checksum-pinned by
the supervisor's maintainer after validation. Do not use upstream's GUI or
self-installer alongside Deadlock Sync.
