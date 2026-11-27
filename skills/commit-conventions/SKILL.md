---
name: commit-conventions
description: "Use when committing, amending, pushing or writing a PR."
---

# Commit and PR conventions (Jaume's standing rules)

These rules apply in every repo and every profile, and are the only copy: other
skills point here instead of restating them. They override the style of recent
commits in a repo when the two differ, except where "Repo exceptions" below
says otherwise.

## Branches

- Never commit on `main` or `master`, in any repo. Create a branch first, then
  commit. Branch from a freshly fetched `origin/main`.

## Approval gate, every time

- Print the full commit message and get an explicit OK before `git commit`.
  The same applies before an amend, a push, and `gh pr create` (show its title
  and body). With several pending commits (a change that spans repos), print
  all of them together and ask one plain question.
- Being handed a ticket number, or a "yes" to an earlier question, is not
  approval of the text. Draft, show, wait.
- Commit, push or open a PR only when the user asks for it. Commit, push and PR
  are separate approvals.

## Message style

- The title-line starts with a capital letter and is 50 characters(or less)
- Plain English: say what the change means and why, not a log of edits.
- Short: a subject plus at most two or three short plain paragraphs, with a
  blank line between subject and body. Start from the short form; for a small
  change one sentence of what it enables and why is enough.
- Subject is imperative. The ticket key never goes in the subject.
- The body says what the change makes possible and why (functionality and
  motivation), not how: no flags, task names, file-by-file edits, test case
  lists, list order, config key names or review-style caveats. The diff shows
  the how. Known limitations and follow-ups go to the user in chat, or as a
  comment on the Jira ticket; offer that instead of putting them in the text.
- Keep internal environment or customer names (staging or production site
  names) out of the message.
- No markdown headings, bold labels or Before/After in the body. Keep the
  trailers the repo uses (for example `Co-Authored-By`).
- The body describes the change itself, not the ticket's background. The
  ticket holds the context.
- Show a shorter draft as soon as the user says "too many details". If the
  motivation is unknown, state the one you assume instead of inventing detail.
- A reusable module or library is described for what it is ("a module that
  creates a DNS zone"), with no mention of the consumer or customer that
  motivated it, in the message and in code comments alike. The consumer belongs
  in the stack or config change that uses it.
- A change that spans repos states its merge order in each commit body.
- After committing or amending, check the stored text with
  `git log -1 --format=%B | cat -e` (macOS `cat` has no `-A`): the blank line
  after the subject shows as a lone `$`.

## Jira ticket

- The ticket goes in a git trailer at the end of the message, as a markdown
  link: `Jira: [GO-123](https://midokura.atlassian.net/browse/GO-123)`, not a
  bare key. Do not wait to be told.
- Use `Jira: NOJIRA` when there is no ticket. Ask for the ticket if it is not
  known; if none is given, commit without one and add it later by amend when it
  arrives.

## One commit per PR

- Fold CI-gate fixes into the existing commit with
  `git commit --amend --no-edit` rather than adding a fixup commit. The message
  stays as approved, so show it again only if its text changes.
- Add follow-up commits only when the user asks to extend the PR.
- Before any force push, `git fetch origin <branch>` and read
  `git log HEAD..origin/<branch>`. Push with `--force-with-lease`; never retry
  with plain `--force`.

## Pull requests

- The PR title is the commit subject. The PR description is the commit message,
  trailer included, unchanged, unless the user asks otherwise:
  `gh pr create --title "$(git log -1 --format=%s)" --body "$(git log -1 --format=%b)"`,
  or `gh pr edit N --body-file -` fed from `git log -1 --format=%b` to fix an
  existing PR. Pass absolute paths to `--body-file`.
- Never put the Jira ticket in the PR title.
- Data may be appended to the PR body later (test results, numbers,
  screenshots); nothing else. One exception: an operator step the change needs
  at deploy time that the plan or diff does not show (for example a
  `tofu apply -replace=...` command) goes in the PR body after the commit body,
  with one sentence of why, when the user picks the PR description as its home.
- A PR that must wait for an external event (DNS delegation, another team)
  opens with `--draft`.
- No reviewers unless the user names them.
- When a ticket arrives after the PR is open: show the new message for each PR
  first, then amend, `git push --force-with-lease`, and `gh pr edit --body` to
  match.

## Repo exceptions

- Check the repo's own CLAUDE.md or contributing notes. Where a repo requires a
  ticket reference on every PR, put
  `Jira: [GO-123](https://midokura.atlassian.net/browse/GO-123)` as the first
  line of the PR body. If the repo requires it in the PR title, put `[GO-123]`
  in front of the commit subject in the title only and keep the body
  trailer-free; the commit keeps its trailer and a ticket-less subject.
