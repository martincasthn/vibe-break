# Environment Check Before Debugging

Many "bugs" are a stopped server, a stale cache, or the wrong host. Confirm each of these before touching code:

- **Process:** is it actually running? On which port?
- **Target:** which host, IP, or URL is real right now? Do not reuse one from memory or old docs without checking.
- **Build:** which build or version is being served? Compare the build ID or version on every host after a deploy.
- **Cache:** could a browser, CDN, Redis, or bundler cache be serving old output? Clear or bypass it before concluding the fix failed.
- **Env:** which env file is loaded, and does the running process or container actually have it?
- **Strays:** lockfiles or config files in parent folders (including the home folder) can change how tools resolve the project root.

Then hit the endpoint or run the command yourself and quote the output. Only ask the user to test after your own check passes.
