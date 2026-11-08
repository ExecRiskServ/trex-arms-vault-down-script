# T.REX ARMS Field Manual Vault — Archival Downloader

Self-contained Bash scripts that mirror the public PDF collection in the
[T.REX ARMS Field Manual Vault](https://www.trex-arms.com/field-manual-vault/)
to local storage. Built for researchers and archivists who want an offline,
resumable copy of the collection.

Each script is **fully self-contained**: the complete list of manuals is
embedded in the file itself as a comment-line manifest, so there are no
external dependencies, no scraping of index pages, and nothing to configure.
Point a script at a destination folder and it works.

Note: 
3,634 files, not 4,215. The vault collapses multiple editions of a manual into one card, and the card links to one edition. The other ~580 editions aren’t exposed in the listing, so this grabs the primary edition of every title. 

## Contents

| Script | Scope | Manuals | Approx. size |
| --- | --- | --- | --- |
| `fmv_download_all.sh` | Every manual in the vault, all eras | 3,634 | ~50–100 GB |
| `fmv_download_1995plus.sh` | Every manual dated 1995 or later, plus undated | 1,901 | ~20–40 GB |

Pick the one that matches the archive you want. The `1995plus` set is the
practical choice for most modern-doctrine research; `all` is the complete
historical mirror.

## Requirements

- macOS, Linux, or any environment with **Bash 4+** and **`curl`**.
- On macOS both are built in — no installation needed.
- Enough free disk space for the set you choose (see table above).

## Usage

```bash
# Make the script executable (first run only)
chmod +x fmv_download_1995plus.sh

# Run it: ./script [destination folder] [parallel downloads]
./fmv_download_1995plus.sh "$HOME/Downloads/Field Manuals" 6
```

Both arguments are optional:

| Argument | Default | Notes |
| --- | --- | --- |
| destination folder | `~/Downloads/FMV_1995plus` (or `FMV_All`) | Created if it doesn't exist. |
| parallel downloads | `4` | 4–6 is a sensible range. Higher is not always faster and is less polite to the host. |

### Output

Files are saved as:

```
NNNN - DESIGNATION - Title (Year).pdf
```

numbered newest-to-oldest, with filesystem-unsafe characters replaced. A
running log is written to `_download_log.txt` in the destination folder,
recording every `OK`, `SKIP`, and `FAIL`.

### Resuming

Both scripts are **safe to re-run**. Already-downloaded files are skipped, so
if a run is interrupted — or some downloads fail — just run the exact same
command again to fetch only what's missing. The summary line at the end of each
run reports how many files were downloaded, skipped, and failed.

## How it works

1. The manifest (one `## num|slug|label` comment line per manual) is embedded at
   the bottom of each script.
2. A pool of `curl` workers fetches each manual's vault page, extracts the
   direct `files.trex-arms.com/pdf/...` link, and downloads the PDF.
3. Downloads run in parallel via `xargs -P`; failures are retried and logged.

There is no authentication, no API key, and no account required — the scripts
only request content that is already publicly served by the vault.

## Be a good citizen

- Keep `parallel downloads` modest (4–6). The goal is a clean personal archive,
  not to hammer anyone's bandwidth.
- Run it once and keep your mirror; re-run only to pick up new additions.

## Credits

Source collection: **[trex-arms.com](https://www.trex-arms.com/field-manual-vault/)**
— T.REX ARMS assembled and hosts the Field Manual Vault.

> This is good content, even if they screwed Lucas over.

The manuals themselves are U.S. Government doctrinal publications and are not
copyrighted works of T.REX ARMS or this project.

## License

The scripts in this repository are released under the [MIT License](LICENSE).
This covers the tooling only, not the downloaded documents.

## Disclaimer

Provided as-is for archival and research purposes. You are responsible for your
own use, your storage, and your bandwidth. This project is not affiliated with
or endorsed by T.REX ARMS.

---

Maintained by **Executive Risk Services (ERS)**.
