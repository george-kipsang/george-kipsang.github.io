# Security Guidelines

Quick instructions and best-practices for handling secrets when working with this repo.

**Never echo secrets to the console**
- Avoid running commands like `echo "mytoken" > file` or printing tokens in plain text. Shell history will record commands by default.

**Use environment variables instead of embedding tokens**
- Set tokens as environment variables (example for `GH_TOKEN`):

export GH_TOKEN="your_personal_access_token_here"

- Use the environment variable in commands (no token text in command line):

mkdocs gh-deploy --clean

- Consider using `direnv`, `vault`, or a CI secrets manager to inject secrets at runtime instead of storing them locally.

**About `~/.zsh_history` and `.gitignore`**
- You asked to add `~/.zsh_history` to `.gitignore`; note that `.gitignore` patterns are relative to the repository. Adding `~/.zsh_history` here will not prevent a file at your home directory from being tracked elsewhere. The real protection is to avoid placing sensitive files inside the repository and to avoid echoing secrets into files in the repo.

**How to clear shell history (Zsh)**
- Clear current session history:

history -c

- Overwrite the history file to remove previous entries:

> ~/.zsh_history

- To remove a specific token line from history (example):

sed -i '/ghp_/d' ~/.zsh_history

After editing `~/.zsh_history`, restart your shell or log out and in so the changes take effect.

**Enable the repository git hooks (recommended)**
- To use the pre-commit hook shipped in this repo:

git config core.hooksPath .githooks
chmod +x .githooks/pre-commit

This sets Git to run hooks from the `.githooks` folder (so the hook is tracked in the repo and shareable).

**If a secret is exposed**
- Revoke the secret immediately (you indicated this token is already invalidated).
- Audit account activity (pushes, GitHub Actions runs, API usage) while the secret was valid.
- Rotate any systems that used the token.

**Why the pre-commit hook is conservative**
- The hook looks for patterns that frequently indicate secrets; false positives are possible. If a commit is blocked but you believe it is a false positive, inspect the staged files and adjust content or unstage the offending file.
