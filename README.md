# Spark Database Fixtures

Compressed MongoDB database fixtures for [Spark](https://github.com/FirelyTeam/spark), published as GitHub Release assets.

> **Note:** Archives are never committed to Git. They exist only as release assets.

## Contents

Each release contains a MongoDB dump (`mongodump --archive --gzip`) for each supported FHIR version, plus a checksum file:

| Asset             | FHIR version               |
|-------------------|----------------------------|
| `stu3.archive.gz` | STU3                       |
| `r4.archive.gz`   | R4                         |
| `r4b.archive.gz`  | R4B                        |
| `r5.archive.gz`   | R5                         |
| `r6.archive.gz`   | R6                         |
| `SHA256SUMS`      | Checksums for all archives |

## Versioning

- Releases use sequential tags: `v1`, `v2`, `v3`, and so on.
- Releases are immutable. If any archive changes, publish a new release. Never use `gh release upload --clobber`.
- The newest release is marked as "Latest" so local development can discover it automatically.
- CI and release builds may pin an explicit tag when reproducibility is required.
- Old releases stay available for as long as any supported Spark revision references them.

## Downloading

**In Spark (recommended):** run Spark's fixture download script:

```bash
FIXTURE_REPOSITORY=IncendiLabs/spark-database-fixtures \
  ./.docker/linux/download-database-fixtures.sh
```

The script resolves the latest release, downloads its `SHA256SUMS`, puts the archives in `.docker/linux/`, skips files that already match their checksum, and replaces corrupt files safely.

Pin a specific fixture release for reproducible CI or release builds:

```bash
FIXTURE_REPOSITORY=IncendiLabs/spark-database-fixtures \
FIXTURE_RELEASE=v1 \
  ./.docker/linux/download-database-fixtures.sh
```

**Direct HTTPS (no authentication needed):**

```text
https://github.com/IncendiLabs/spark-database-fixtures/releases/latest/download/r4.archive.gz
```

**GitHub CLI:**

```bash
gh release download \
  --repo IncendiLabs/spark-database-fixtures \
  --pattern '*.archive.gz' \
  --dir .docker/linux
```

Pass a tag such as `v1` after `download` to select a specific release.

**Verify checksums:**

```bash
sha256sum -c SHA256SUMS
```

## Publishing a new release

Run this from a Spark checkout that has every archive (including `r6.archive.gz`). Replace `vN` with the next release tag:

```bash
fixture_tmp="$(mktemp -d -p .)"
sha256sum .docker/linux/*.archive.gz > "$fixture_tmp/SHA256SUMS"

gh release create vN \
  .docker/linux/*.archive.gz \
  "$fixture_tmp/SHA256SUMS" \
  --repo IncendiLabs/spark-database-fixtures \
  --title "Spark database fixtures vN" \
  --notes "MongoDB fixtures for Spark's supported FHIR versions." \
  --latest
```

The Spark download script will discover the new latest release automatically. Update any explicitly pinned `FIXTURE_RELEASE` value in Spark through a normal pull request when CI or a release build should move to the new fixture set.

## Data policy

These fixtures are public. They must not contain credentials, personal data or other sensitive information.
