# spark-database-fixtures

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
- Releases are never marked as "Latest". Consumers pin an explicit version.
- Old releases stay available for as long as any supported Spark revision references them.

## Downloading

**In Spark (recommended):** run Spark's fixture download script. It reads the pinned tag and checksums from Spark's manifest, puts the archives in `.docker/linux/`, skips files that already match their checksum, and replaces corrupt ones.

**Direct HTTPS (no authentication needed):**

```text
https://github.com/IncendiLabs/spark-database-fixtures/releases/download/v1/r4.archive.gz
```

**GitHub CLI:**

```bash
gh release download v1 \
  --repo IncendiLabs/spark-database-fixtures \
  --pattern '*.archive.gz' \
  --dir .docker/linux
```

**Verify checksums:**

```bash
sha256sum -c SHA256SUMS
```

## Publishing a new release

Run this from a Spark checkout that has every archive (including `r6.archive.gz`). Replace `vN` with the next release tag:

```bash
fixture_tmp="$(mktemp -d)"
sha256sum .docker/linux/*.archive.gz > "$fixture_tmp/SHA256SUMS"

gh release create vN \
  .docker/linux/*.archive.gz \
  "$fixture_tmp/SHA256SUMS" \
  --repo IncendiLabs/spark-database-fixtures \
  --title "Spark database fixtures vN" \
  --notes "MongoDB fixtures for Spark's supported FHIR versions." \
  --latest=false
```

Then open one Spark pull request that updates the pinned tag and SHA-256 digests in the fixture manifest.

## Data policy

These fixtures are public. They must not contain credentials, personal data or other sensitive information.
