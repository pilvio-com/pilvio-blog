# Next Session — pilvio-blog

**Resume from:** branch `main` at commit `76f14a1`
**Last session:** 2026-06-04, full record at `.claude/handoffs/2026-06-04-2215-publish-two-articles.md`

---

## Current state

Two articles shipped and pushed: SharePoint-vs-Nextcloud-AI and Terraform-Nextcloud-Pilvio production guide. Working tree has one untracked file `posts/andmesuveraanne-ai-malu.md` that has no frontmatter yet — it was deliberately deferred from the previous session.

---

## Next task

Process `posts/andmesuveraanne-ai-malu.md`: locate a matching illustration in `~/Nextcloud/Marketing/blogi-artiklid/` (if any), add frontmatter, push to `main`.

---

## Key files

- `posts/andmesuveraanne-ai-malu.md` — untracked article awaiting frontmatter + publish
- `~/Nextcloud/Marketing/blogi-artiklid/` — source folder for article + illustration originals
- `README.md` — repo frontmatter conventions (Estonian date, required fields, image path format)
- `.claude/handoffs/2026-06-04-2215-publish-two-articles.md` — full previous-session record

---

## Blockers / waiting

- (none)

---

## Do not regress

- Use Estonian date format in `publishedAt` (e.g. `"21. mai 2026"`).
- `imageUrl` must be `/media/<name>.png` (leading slash, repo-relative).
- Always offer shortened image filenames via AskUserQuestion when copying from Nextcloud folder — do not reuse long `-illustration.png` originals. See project memory `image_naming_preference.md`.
- `git pull --rebase origin main` before push — the sync workflow file occasionally gets remote auto-commits.
- Only one post should have `featured: true` at a time; default new posts to `featured: false`.
