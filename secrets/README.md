# Encrypted secrets

This profile uses [age](https://age-encryption.org) for any secrets shipped in `MANIFEST.md` under `secrets:` blocks.

`age.pub` here is the **public** key. Anyone can encrypt to it; only the owner of the matching private key (kept at `~/.config/boothub/age.key` on the owner's machine) can decrypt.

## How agents handle `secrets:` on install

1. The agent sees a `secrets:` block in a bundle.
2. It looks for a private age key at `~/.config/boothub/age.key`.
3. If found → decrypt and prompt the user to confirm before writing the resulting env var anywhere.
4. If not found → fall back to `env_required:` (the agent prompts the user to set the value manually).

## Why this lives in a public repo

Encrypted secrets in a public profile only make sense for **you re-bootstrapping your own machines**. Anyone else fetching your profile sees an opaque blob and gets the manual-env fallback.

## Generating your own keypair

```bash
brew install age
mkdir -p ~/.config/boothub
age-keygen -o ~/.config/boothub/age.key
chmod 600 ~/.config/boothub/age.key
grep '^# public key:' ~/.config/boothub/age.key
# copy that public key to secrets/age.pub in your profile repo
```

## Encrypting a value

```bash
echo "$YOUR_TOKEN" | age -r "$(cat secrets/age.pub)" -a
# paste the resulting block into MANIFEST.md under the bundle's secrets:
```
