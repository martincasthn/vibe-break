# Safe Edits on Windows and Mixed Environments

- Edit files with the editor or patch tool, not shell rewrites (`Set-Content`, `Out-File`, `sed -i`, echo redirects). Shell rewrites can silently change encoding or line endings.
- After an edit, check the diff size. A one-line change that shows thousands of changed lines is an encoding or line-ending accident. Stop and fix it before continuing.
- Keep files UTF-8. If the project has no `.gitattributes` or `.editorconfig`, suggest one that fixes line endings and charset (for example `* text=auto eol=lf`).
- Launcher and setup scripts should keep the window open on error so the user can read what failed.
- Watch for path issues: spaces in paths, backslashes in config files, and case-insensitive file names that clash on Linux deploys.
