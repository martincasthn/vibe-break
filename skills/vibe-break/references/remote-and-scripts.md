# Remote Commands and Automation Scripts

DevOps failures are often not logic bugs. They are a wrong exit code, a mangled line ending, a missing slash, or a prompt nobody could answer.

## Secrets and keys

- Never write a password, token, private key, or `.env` value into a script, command line, log, or chat. Read it at runtime from an environment variable, a secret store, or an interactive prompt, and refer to it by name.
- Reference SSH keys by path (`key_filename=os.environ["SSH_KEY_PATH"]`), never by pasting key content. Keep `.pem` files out of the repo and readable only by the user (`chmod 600`; on Windows, restrict with `icacls`).
- Do not silently disable host key checking (`StrictHostKeyChecking=no`, Paramiko `AutoAddPolicy`) on real servers. If it is needed for a first connection, say so and let the user decide.

## Exit codes

- A command that "ran" is not a command that succeeded. Check the real exit status every time.
- Paramiko: `exec_command` returns immediately. Read `stdout.channel.recv_exit_status()` and stderr before deciding it worked.
- PowerShell: use `$LASTEXITCODE` for native programs; `$?` alone can mislead. In a `.ps1`, end with `exit $code` explicitly.
- Batch: use `exit /b <code>`. `call` other `.bat` files, or control never returns.
- Bash: start scripts with `set -euo pipefail`; without `pipefail`, a failing command in a pipe reports success.
- Node / Python: exit with a non-zero code on failure (`process.exit(1)`, `sys.exit(1)`), not just a printed error.

## Line endings, encoding, and paths

- Scripts that run on Linux must use LF. A CRLF shell script fails with `bad interpreter` or `$'\r': command not found`. Enforce with `.gitattributes` (`*.sh text eol=lf`).
- Windows `.bat` and `.cmd` files are safest with CRLF; mark them `eol=crlf` in `.gitattributes`.
- Keep scripts UTF-8 without BOM, with one exception: Windows PowerShell 5.1 reads a BOM-less `.ps1` as the legacy code page, so a `.ps1` containing non-ASCII characters (accents, symbols) needs UTF-8 with BOM, or should stay ASCII-only. PowerShell 7+ reads BOM-less UTF-8 correctly. Also note that 5.1's `Out-File` and `Set-Content` can add a BOM or use the wrong code page when writing.
- Trailing slashes change meaning: `rsync src/ dest` copies contents, `rsync src dest` copies the folder. Check every path in `scp`, `rsync`, and `cp -r`.
- Do not send Windows paths (backslashes, drive letters) to a Linux host. Quote paths with spaces. Watch nested quoting in `ssh host "cmd"` and `cmd /c "..."`.

## When a command cannot run interactively

If a step needs a prompt the agent cannot answer (sudo password, passphrase, host key confirmation, MFA), do not retry in a loop or try to bypass it. Instead:

1. Write the command as a local script file in the project (for example `scripts/deploy.ps1` or `scripts/connect.sh`).
2. Have the script read every secret from environment variables or prompt for it at runtime. No values in the file.
3. Tell the user exactly how to run it and which environment variables it expects, by name only.
4. After the user runs it, read the output they share and verify the result before moving on.

## Before running anything remote

- Say which host, user, and directory the command targets, and confirm it is the intended environment (staging vs production).
- Destructive remote commands (`rm -rf`, service restarts in production, database commands) follow SKILL.md Section 7: restate what changes and wait for a yes.
