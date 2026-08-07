# AI Ada — stake pool site & metadata

Public site and on-chain metadata for the **AI Ada (AIADA)** Cardano stake pool.

| | |
|---|---|
| Pool ID (bech32) | `pool1uvzzcc9vh9fs2e3j0dg20cumxxqxfjtdqc6cvzszrqexq6a3uuj` |
| Pool ID (hex) | `e3042c60acb9530566327b50a7e39b318064c96d0635860a02183260` |
| Metadata URL | `https://ai-ada.github.io/poolMetaData.json` |
| Metadata hash | `aacec8f320bb390b3b35c88b37975ee0d685858a8dc8c1aabb65d2fd5e9a4ed5` |

## Files

- `index.html` — the public landing page
- `poolMetaData.json` — the metadata file referenced by the pool registration
  certificate. 300 bytes (limit is 512), all fields within their length caps.

## Deploying to GitHub Pages

> **Run `git init` in *this* directory only — never in `~/adanode`.**
> The parent directory holds `pool-cold/cold.skey` (the pool's root private
> key) and `keyfile.ppk`. A repo initialised one level up would push both to a
> public GitHub repo, and a leaked cold key means the pool identity is gone
> for good. Confirm with `pwd` before the first `git add`.

The repo **must** be named `ai-ada.github.io` (matching the account name) — that
is what produces the short root-level URL. A project repo would give you
`https://ai-ada.github.io/some-repo/poolMetaData.json`, which is longer and
eats into the 64-character on-chain URL limit.

1. Create the GitHub account `ai-ada`, then a **public** repo named
   `ai-ada.github.io`.
2. From this directory:

```bash
git init && git add . && git commit -m "AI Ada pool site and metadata" && git branch -M main && git remote add origin git@github.com:ai-ada/ai-ada.github.io.git && git push -u origin main
```

3. In the repo: **Settings → Pages → Source: Deploy from a branch → `main` / `root`**.
4. Wait a couple of minutes, then verify the metadata file is served as raw JSON:

```bash
curl -sS https://ai-ada.github.io/poolMetaData.json
```

## Verifying before you register

The hash in the registration certificate must match the hash of the file as
actually served. Check that end to end:

```bash
curl -sS https://ai-ada.github.io/poolMetaData.json -o /tmp/fetched.json && cardano-cli conway stake-pool metadata-hash --pool-metadata-file /tmp/fetched.json
```

That must print `aacec8f320bb390b3b35c88b37975ee0d685858a8dc8c1aabb65d2fd5e9a4ed5`.
If it doesn't, GitHub served something different from what's committed here —
resolve that before spending any ADA.

## If the GitHub account name differs

`ai-ada` was assumed because GitHub usernames can't contain spaces. If you
register a different name, three things change together:

1. `homepage` in `poolMetaData.json`
2. `og:url` and the `<title>`/meta description in `index.html` (cosmetic)
3. **The metadata hash** — `homepage` lives inside the JSON, so changing it
   changes the file, which changes the hash. Recompute it with
   `cardano-cli conway stake-pool metadata-hash` and use the new value.

Keep the new metadata URL under 64 characters.

## Not done yet

On-chain registration. That needs the registration certificate, pledge/cost/
margin decided, a 500 ADA deposit, and a signature from the cold key in
`../pool-cold/`. Real money — review and submit it yourself.
