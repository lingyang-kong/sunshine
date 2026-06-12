# Sunshine APT Repository

Signed APT repository for upstream [LizardByte/Sunshine](https://github.com/LizardByte/Sunshine)
release packages.

The repository mirrors upstream stable `.deb` assets and republishes them as a
GitHub Pages APT archive. `apt` then handles installation and upgrades in the
normal way.

## Layout

- `scripts/sync-apt-repo.sh`: builds the archive.
- `templates/`: checked-in site and manifest templates.
- `dist/`: generated GitHub Pages site.
- `dist/dists/` and `dist/pool/`: conventional APT metadata and package storage.

## What It Publishes

- Stable upstream releases only.
- All upstream Debian and Ubuntu `.deb` assets whose filenames match
  `sunshine-<suite>-<arch>.deb`.
- The newest complete releases that fit within the 1,000,000,000-byte GitHub
  Pages site limit.

Current suite names follow upstream naming, for example:

- `ubuntu-22.04`
- `ubuntu-24.04`
- `ubuntu-26.04`
- `debian-trixie`

## Automation

GitHub Actions rebuilds the repository every Monday and Thursday at 06:17 UTC.

The workflow:

1. Reads stable releases from `LizardByte/Sunshine`.
2. Calculates how much space a new release would add and evicts the oldest
   retained whole releases until it fits.
3. Downloads those packages.
4. Generates suite-specific `Packages`, `Release`, `InRelease`, and
   `Release.gpg` metadata.
5. Publishes the snapshot from `dist/` to GitHub Pages.

Retained releases coexist under `pool/main/s/sunshine/<suite>/` without
overwriting each other.

## Client Install

Example for Ubuntu 22.04 `amd64`:

```sh
curl -fsSL 'https://lingyang-kong.github.io/sunshine/sunshine-archive-keyring.gpg' \
  | sudo tee /usr/share/keyrings/sunshine-archive-keyring.gpg >/dev/null

echo 'deb [signed-by=/usr/share/keyrings/sunshine-archive-keyring.gpg] https://lingyang-kong.github.io/sunshine ubuntu-22.04 main' \
  | sudo tee /etc/apt/sources.list.d/sunshine.list

sudo apt update
sudo apt install sunshine
```

Use the suite that matches the upstream package target for the host. For this
machine, that is `ubuntu-22.04`.
