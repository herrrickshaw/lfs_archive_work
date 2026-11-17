# lfs_archive_work — manifests from the 2026-08-02 Git LFS store archive

Record of a one-off maintenance job: the owner's `$HOME` git repository had a 5.5 GB local Git LFS
object store, part of which was never pushed to the LFS remote. On 2 August 2026 the store was
inventoried, compressed with zstd, uploaded to off-machine storage, verified, and then pruned
locally. This repository keeps the inventory files, the checksum of the archive and the job log so
the archive can be verified and the pruned objects traced later. The 3.8 GB archive itself is not
here.

> Status: archived snapshot (data-only). No scripts; the files are outputs of a job that ran once.

> **New here?** See [How to use this repo with AI (Claude, Gemini, ChatGPT)](HOW_TO_USE_WITH_AI.md).

## What it contains
- `local_oids_paths.txt` — 11,300 lines, one absolute path per LFS object that was in
  `~/.git/lfs/objects` before the prune.
- `local_only_oids.txt` — 344 object IDs (SHA-256) that existed locally but were **not** on the LFS
  remote; the log flags these as unrecoverable if lost, which is why the archive was made.
- `lfs_store_20260802.tar.zst.sha256` — SHA-256 of the archive
  (`8eb4e1a5…1608ead`, 4,074,439,946 bytes) in `sha256sum` format.
- `lfs_archive.log` — timestamped log of the four stages: verify, archive, upload, prune.

## What the job did

| Stage | Result recorded in `lfs_archive.log` |
|---|---|
| verify | 11,300 objects, 5.5 GB local; 344 local-only |
| archive | `zstd -T0` tarball of `~/.git/lfs/objects`, 3.8 GB, SHA-256 recorded |
| upload | 1 file transferred in 7 m 48 s; remote size matched (4,074,439,946 bytes) |
| prune | 332 objects retained, 18 verified against remote, 10,970 deleted; store 5.5 GB → 2.4 GB |

## Verifying the archive
```bash
# with the tarball restored next to the checksum file (paths in the .sha256 are absolute)
sha256sum -c lfs_store_20260802.tar.zst.sha256
```
To check whether a given object was local-only, grep its OID in `local_only_oids.txt`.

## Repository layout

| Path | Purpose |
|---|---|
| `lfs_archive.log` | Job log, 15 lines |
| `local_oids_paths.txt` | Pre-prune inventory (paths) |
| `local_only_oids.txt` | Objects absent from the LFS remote |
| `lfs_store_20260802.tar.zst.sha256` | Archive checksum |
| `HOW_TO_USE_WITH_AI.md` | How to read the repo with an AI assistant |
| `graphify-out/` | Generated knowledge-graph cache for the `graphify` tool; not hand-edited |
| `.github/workflows/quality-gates.yml` | gitleaks secret scan on push / PR |

## Limitations and known issues
- The upload destination is not named in the log; the archive location must be taken from the
  owner's backup notes, not from this repository.
- Paths in the manifests are absolute (`/Users/umashankar/...`) and specific to the machine the job
  ran on.
- The script that produced these files is not included, so the job cannot be re-run from here.

## Licence
No licence file yet — all rights reserved.
