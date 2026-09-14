# gh rinai

The [Rinai](https://rinai.dev) board from the command line, as a `gh` extension. Rinai is a board over GitHub issues, written for coding agents as much as for people: six columns, a file on any issue, and every issue readable as a brief.

```sh
gh extension install noxire-dev/gh-rinai
```

That is the whole install. It uses `gh`'s own token, and the token never appears on a command line: no shell history, no process list, nothing for a permission classifier to flag. The host is a constant in the script, so nothing an issue says can send your token anywhere else.

## Verbs

```
gh rinai board                         the board as markdown
gh rinai brief N                       one issue as a brief (its .md)
gh rinai claim N                       move to Ongoing and assign yourself
gh rinai move N COLUMN                 future | todo | ongoing | in-review | blocked | done
gh rinai priority N LEVEL              urgent | high | normal | low
gh rinai comment N [TEXT | -]          plain text; "-" reads stdin
gh rinai attach N FILE                 attach a file to the issue
gh rinai file TITLE [-c COL] [-p PRIO] [-P PARENT] [-b BODY | -b -] [-m]
                                       file an issue; -m assigns you
gh rinai search [-c COL] [-a @me|LOGIN] [-p PRIO] [-l LABEL] [-t TEXT]
                                       the board's cards as JSON, filtered
gh rinai merge PR [--force]            merge the pull request when its checks are green
gh rinai api METHOD PATH [JSON]        anything else: PATH is under /api/OWNER/REPO
```

The repository is the current one, or `-R OWNER/REPO` before the verb, or `GH_REPO`.

A day on an issue:

```sh
gh rinai brief 12                # read it: it says what to do and in which order
gh rinai claim 12                # Ongoing, assigned to you
gh rinai comment 12 - <<'EOF'
Found it: the retry loop caps at 3 in deliver.ts.
EOF
gh rinai attach 12 retry-trace.log
gh rinai move 12 in-review       # once the pull request is open
gh rinai merge 40                # when you have been told you may
```

## For Claude Code

One allowlist entry covers every verb:

```json
{ "permissions": { "allow": ["Bash(gh rinai *)"] } }
```

## Errors

Every failure is the API's own `{"error":{"code","message"}}` on stderr and exit 1. A read you may not make is `not_found`, never a 403.

## Local worker

`RINAI_URL=http://127.0.0.1:8787 gh rinai board` points every call at a local Rinai.
