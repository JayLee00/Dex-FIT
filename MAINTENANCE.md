# DexFIT — Maintenance & Double-Blind Workflow

> **Personal reference.** This file lives in the personal landing-page
> repository (`JayLee00/Dex-FIT`) and is **not** mirrored into the
> anonymized project-page repository. Never copy the content of this
> file — nor any other text that mentions the `JayLee00` account — into
> the project page repository, as that would break double-blind review.

---

## Repository topology

| Purpose | Repository | Live URL |
|---|---|---|
| Personal landing page (public, identifies author) | `JayLee00/Dex-FIT` | `https://jaylee00.github.io/Dex-FIT/` |
| Anonymous project page (double-blind, shown to reviewers) | `project-robotics/DexFIT` | `https://project-robotics.github.io/DexFIT/` |

The landing page contains only the paper title and a **"Project Page"**
button that redirects to the anonymous project page. No names,
affiliations, or identifying links appear on the anonymous side.

## Local working directories

| Repo | Local path |
|---|---|
| `project-robotics/DexFIT` | `C:\Users\JAY\01_Research\08_GitHub\DexFIT\DexFIT\` |
| `JayLee00/Dex-FIT` | `C:\Users\JAY\01_Research\08_GitHub\DexFIT_upload\` |

Keeping the two clones in different folders is the primary safeguard
against accidentally pushing identifying content to the anonymous repo.

## Two-account Git authentication

Git Credential Manager is configured with:

```bash
git config --global credential.useHttpPath true
```

This makes git look up cached credentials **per full repo path**, so
the two accounts never collide.

| Credential key | Account | Purpose |
|---|---|---|
| `git:https://github.com/project-robotics/DexFIT.git` | `project-robotics` | PAT cached — auto push |
| `git:https://github.com/JayLee00/Dex-FIT.git` | `JayLee00` | Cached after first push |

### If a push prompts for auth

Git / GCM will open a browser window. **Make sure the browser is
logged in as the account that owns the target repository before
clicking Authorize.** Otherwise the wrong account's token will be
cached and the push will return HTTP 403.

Recovery if the wrong account gets cached:

```powershell
cmdkey /delete:git:https://github.com/<owner>/<repo>.git
```

Then retry the push and authenticate as the correct account.

### Revoking a PAT

When no longer needed, revoke at
<https://github.com/settings/tokens> (while logged in as the owning
account). The local cache can be cleared with the `cmdkey` command
above.

## Double-blind rules for the project page

The anonymous repo (`project-robotics/DexFIT`) is the one reviewers
may see. Everything that lands there must be free of identifying
information. In particular, **do NOT commit** any of:

- Author names (given/family/nickname/handle).
- Institutional affiliation (university, lab, company, funding agency,
  grant number).
- Hardware identifiers that uniquely identify a group's platform
  (internal model names, proprietary serials, unpublished product
  codenames). Prefer generic descriptions such as
  *"16-DoF multi-sensory robot hand"*.
- Personal contact info (emails, phone numbers, personal websites,
  social handles).
- Acknowledgements to specific individuals or groups.
- Links to personal/lab repositories that reveal identity. Only the
  anonymous preprint / anonymous code portal from the paper is safe.
- Commit authorship (`user.name` / `user.email`) that leaks identity —
  set a local anonymized value inside the project-page working tree.

### Git authorship hygiene

Inside the anonymous repository clone:

```powershell
cd C:\Users\JAY\01_Research\08_GitHub\DexFIT\DexFIT
git config user.name  "project-robotics"
git config user.email "<anonymous contact>"
```

Do not enable global `user.name` / `user.email` with the real name
while working on this repo.

### Media assets

- Videos / GIFs must not show printed lab banners, name tags, office
  nameplates, or posters identifying institutions.
- Screen captures should not include desktop wallpapers, file paths,
  usernames, calendar entries, or terminal prompts that reveal
  identity.
- Audio (if any) must not include recognizable voices.

### Large-file policy

GitHub rejects any single file > 100 MB. Two tactile-visualization
GIFs in the source material exceed this limit; they are listed in
`.gitignore` inside the anonymous repo and swapped for
*"Video coming soon"* placeholders in `index.html`. If they are
needed on the live page later, host them externally (e.g. Hugging
Face Spaces) or enable Git LFS **on a fresh commit** to avoid
rewriting the existing history.

## Typical push workflow

```powershell
# Edit the anonymous project page
cd C:\Users\JAY\01_Research\08_GitHub\DexFIT\DexFIT
git add .
git commit -m "..."
git push origin main   # → project-robotics

# Edit the personal landing page
cd C:\Users\JAY\01_Research\08_GitHub\DexFIT_upload
git add .
git commit -m "..."
git push origin main   # → JayLee00
```

## Pre-push checklist (anonymous repo)

- [ ] No author / affiliation strings anywhere under `DexFIT\DexFIT\`.
- [ ] `git log` author is not the real name.
- [ ] No media frames reveal lab banners, badges, or screens.
- [ ] `README.md`, `DOUBLE_BLIND.md`, and `index.html` contain only
      anonymous contact paths (anonymous preprint / anonymous code
      portal).
- [ ] Commit message does not reference the real author, lab, or
      personal repositories.

---

*This maintenance file stays in the personal repository only. It will
be removed or archived once the double-blind review cycle closes.*
