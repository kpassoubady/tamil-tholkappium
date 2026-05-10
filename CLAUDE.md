# CLAUDE.md

Guidance for Claude Code when working in this repository.

## Project purpose

`tamil-tholkappium` is a curated digital library of **Tolkappiyam** (தொல்காப்பியம்) — the foundational Tamil grammar text — and its commentaries, study editions, and research works. It is a **content repo**, not a software project. The deliverable is the curated `README.md` index pointing at PDFs hosted as GitHub Release assets.

## Structure

- `README.md` — Bilingual (English + Tamil) catalog. The canonical index, grouped by:
  1. Ezhuthadhikaram (எழுத்ததிகாரம்) — phonology
  2. Solladhikaram (சொல்லதிகாரம்) — morphology/syntax
  3. Poruladhikaram (பொருளதிகாரம்) — semantics/poetics
  4. General & Commentaries
  5. Research & Other Resources
- `CLAUDE.md` — This file.
- `.gitignore` — Excludes local PDFs and working files. **PDFs are NOT committed to the repo.**
- GitHub Release `v1.0.0` — Hosts all 40 PDFs as downloadable assets. Source of truth for downloads.

Local PDFs in the working directory are working copies for the maintainer — they stay untracked.

## Naming convention for release assets

Format: `thol_book_NN-<slug>.pdf`

- `NN` — Zero-padded sequence number (`01`–`40`+). Preserves historical ordering and stays stable across renames.
- `<slug>` — Lowercase, hyphen-separated descriptive slug. Include in this order when known:
  1. **Section** — `ezhuthadhikaram`, `solladhikaram`, `porulathigaram`, `meyppattiyal`, `kalaviyal`, `seyyuliyal`, etc.
  2. **Sub-topic/volume** — `thogudhi-1`, `part-1`, `agathinaiyiyal`.
  3. **Author or editor** — transliterated, hyphenated: `ilampuranar`, `nachinarkkiniyar`, `vellaivaranan`, `thamizhannal`, `karunanidhi`, `sundaramurthi`.
  4. **Year** — 4-digit publication year if known: `1924`, `1985`, `2008`.
  5. **Archive ID** — fallback identifier when other metadata is missing: `bok-0001588`, `stam13`, `pm0100`.

Rules:
- No Unicode, no spaces, no underscores inside the slug (underscore only in the `thol_book_NN` prefix).
- Transliterate Tamil words rather than translating them (`meyppattiyal`, not `expressions`).
- Skip missing metadata silently — better a short clean slug than a placeholder.
- Examples:
  - `thol_book_07-ezhuthadhikaram-ilampuranar-textbook-muvendhan.pdf`
  - `thol_book_32-porulathigaram-thogudhi-1-thamizhannal.pdf`
  - `thol_book_19-tholkappiyam-1985-ilampuranar-urai.pdf`

## Workflow: adding a new book

1. **Pick the next number.** Use the lowest unused `NN` (currently next is `41`).
2. **Build the filename** per the naming convention above.
3. **Upload to the release**:
   ```sh
   gh release upload v1.0.0 /path/to/local.pdf --repo kpassoubady/tamil-tholkappium \
     --clobber --name "thol_book_41-<slug>.pdf"
   ```
   `--name` renames during upload; `--clobber` lets you re-upload if a same-named asset exists.
4. **Add it to `README.md`** under the correct section (Ezhuthadhikaram / Solladhikaram / Poruladhikaram / General / Research). Link text should be the Tamil title (with optional English qualifier in parentheses).
5. **Verify the download URL** by clicking the link or `curl -IL` against it.

## Workflow: renaming a release asset

Use the GitHub API (don't delete + re-upload — keeps the asset ID and download stats):
```sh
gh api -X PATCH /repos/kpassoubady/tamil-tholkappium/releases/assets/<ID> -f name=<new-name>
```
Then update the link in `README.md` in the same commit. Old URLs stop working — call this out in the commit message if it's a breaking rename.

## Git & commit guidelines

- **Never commit PDFs.** `.gitignore` excludes `*.pdf` at the repo root. PDFs live in Releases only.
- **Never re-introduce Git LFS.** It was removed in commit `9904037` in favor of Releases — Releases give better direct-download UX and don't burn LFS bandwidth.
- **Commit messages**: short subject line in imperative mood, focused on *why*. Match existing style (`Update README with direct links to Release assets`, `Remove Git LFS and .gitattributes in favor of GitHub Releases`). No co-author trailers.
- **One logical change per commit**: rename + README update belong together; adding a book is its own commit.
- **Branches**: work directly on `main` is fine for this content repo — there are no CI gates or reviewers.

## Things to avoid

- Don't translate Tamil titles in `README.md` — keep them in Tamil script. English qualifiers in parentheses are fine for disambiguation (e.g., `(Standard Text)`, `(1985)`).
- Don't reorder existing `thol_book_NN` numbers — they're referenced externally.
- Don't create per-section subdirectories for PDFs in the repo. The release is flat by design.
