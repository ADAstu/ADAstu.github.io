# AI Ada — stake pool site & metadata

Public site and on-chain metadata for the **AI Ada (AIADA)** Cardano stake pool.

| | |
|---|---|
| Pool ID (bech32) | `pool1uvzzcc9vh9fs2e3j0dg20cumxxqxfjtdqc6cvzszrqexq6a3uuj` |
| Pool ID (hex) | `e3042c60acb9530566327b50a7e39b318064c96d0635860a02183260` |
| Site | `https://adastu.github.io` |
| Metadata URL | `https://adastu.github.io/poolMetaData.json` |
| Metadata hash | `fb78d9f37fc71e5bb1d465507e926f0ed286fbf4c80f79de88bbaab7045359e4` |

## Files

- `index.html` — the public landing page
- `poolMetaData.json` — the metadata file referenced by the pool registration
  certificate. 300 bytes (limit 512), all fields within their length caps.

## Repo layout

This is a GitHub **user site**: the repo is named `ADAstu.github.io`, matching
the account name exactly, so Pages serves it at the domain root. That keeps the
metadata URL at 42 characters, well inside the 64-character on-chain limit.

> **Only ever run `git init` in this directory — never in `~/adanode`.**
> The parent directory holds `pool-cold/cold.skey` (the pool's root private key)
> and `keyfile.ppk`. A repo initialised one level up would push both to a public
> GitHub repo, and a leaked cold key means the pool identity is gone for good.

## Enabling Pages

Repo → **Settings → Pages → Source: Deploy from a branch → `main` / `/ (root)`**.
First build takes a couple of minutes.

## Verifying before you register

The hash in the registration certificate must match the hash of the file **as
actually served**, not just the local copy. Check end to end:

```bash
curl -sS https://adastu.github.io/poolMetaData.json -o /tmp/fetched.json && cardano-cli conway stake-pool metadata-hash --pool-metadata-file /tmp/fetched.json
```

That must print:

```
fb78d9f37fc71e5bb1d465507e926f0ed286fbf4c80f79de88bbaab7045359e4
```

If it doesn't, GitHub is serving something different from what's committed here.
Resolve that before spending any ADA.

## If anything in the metadata changes

`homepage` lives inside `poolMetaData.json`, so changing the URL — or the name,
ticker, or description — changes the file, which changes the hash. Recompute it:

```bash
cardano-cli conway stake-pool metadata-hash --pool-metadata-file poolMetaData.json
```

Then use the new value in the registration certificate. Before registration this
is free to change; afterwards it needs a pool re-registration transaction.

## Deploy

```bash
git push origin main
```

Authenticated with the dedicated key `~/.ssh/github_aiada` (separate from the
block producer's `~/.ssh/cardano_bp`; `~/.ssh/config` pins it per-host with
`IdentitiesOnly yes`).

## Status

Registered on mainnet 2026-08-07 (epoch 647). Both relays are registered
on-chain as of the 2026-08-16 re-registration, live as of the epoch
649→650 boundary (2026-08-18).
