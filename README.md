# ipvault

The `ipvault` command-line tool for [IP Vault](https://ip.rev3rse.dev): seal your app's models,
data and content so only licensed installations can open them, publish them from CI, and check
that a built app ships nothing in the clear.

This repository publishes release binaries. They are what the IP Vault GitHub Action and GitLab
CI/CD components download, and each integration refuses to run an archive whose SHA-256 is not
pinned in its own files.

## Install

Pick the archive for your platform from [Releases](../../releases), check it, and extract it:

```sh
v=0.2.1
t=x86_64-unknown-linux-musl   # or aarch64-unknown-linux-musl, aarch64-apple-darwin, x86_64-apple-darwin
curl -fsSLO "https://github.com/rev3rse-dev/ipvault-cli/releases/download/v$v/ipvault-$v-$t.tar.gz"
curl -fsSLO "https://github.com/rev3rse-dev/ipvault-cli/releases/download/v$v/SHA256SUMS"
sha256sum -c SHA256SUMS --ignore-missing      # macOS: shasum -a 256 -c SHA256SUMS --ignore-missing
tar -xzf "ipvault-$v-$t.tar.gz"
"./ipvault-$v-$t/ipvault" --version
```

The Linux binaries are static and run on any distribution, Alpine included. Publishing calls go
through the system's `curl`. The macOS binaries are not signed or notarized: a copy downloaded
with a browser needs `xattr -d com.apple.quarantine ipvault` before it runs.

## Commands

| Command | |
|---|---|
| `ipvault release plan` | Validate `ipvault.toml` and show the release it would make. No credential, no network |
| `ipvault release publish` | Seal each declared asset on this machine and register its key with IP Vault. The plaintext never leaves the machine |
| `ipvault release verify` | Check a built package: every sealed output is in it, and no plaintext input is, whole or in part |
| `ipvault release abandon` | Give up a release that was begun and will not be registered |
| `ipvault scan` | Find models an app package ships in plaintext. No account needed |
| `ipvault seal`, `ipvault inspect` | Seal one file; show a sealed file's header |
| `ipvault app identity` | Read what an app registers with IP Vault from its APK or AAB: the package name and the SHA-256 of each signing certificate. For a bundle, that is the upload key: Play-installed devices present Play's app signing key, shown in Play Console |

`publish` authenticates with the CI job's own OIDC identity on GitHub Actions and GitLab CI, or
with a CI token in `IPVAULT_TOKEN`; never with a command-line argument. The
[integration guide](https://ip.rev3rse.dev/docs/integration) covers the manifest, both CI
providers and the SDK; the [API reference](https://ip.rev3rse.dev/docs/api) covers the calls.

Before 1.0, commands and formats may change between minor versions.

## Licence

Apache License 2.0; see [LICENSE](LICENSE). Each archive also carries THIRD-PARTY-NOTICES.txt: the
open-source crates the binary includes, with their licences.
