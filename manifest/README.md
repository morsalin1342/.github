# Shared manifest for the sibling repositories

`images.yaml` holds the facts that `apache-docker`, `caddy-docker`, `frankenphp-docker`,
`nginx-docker`, `php-docker` and the Souin cache plugin all state about each other: project names,
GitHub owner, Docker Hub namespaces, one-line descriptions and Docker Hub short descriptions.
Each fact lives here once. The repositories do not retype it.

`sync_docs.py` is how a repository follows the manifest.

```bash
python3 manifest/sync_docs.py --manifest manifest/images.yaml --repo caddy-docker --dir ../caddy-docker --write
python3 manifest/sync_docs.py --manifest manifest/images.yaml --repo caddy-docker --dir ../caddy-docker --check
```

`--write` rewrites the generated blocks (between `<!-- BEGIN GENERATED: related ... -->` and
`<!-- END GENERATED: related -->`) in the repository's READMEs and Docker Hub READMEs. `--check`
changes nothing and exits 1 on drift: a stale block, a Docker Hub short description or a Dockerfile
label that disagrees with the manifest, or a README that names a namespace or owner the manifest
does not know. Every repository runs `--check` on each pull request (`.github/workflows/docs-check.yml`).

## What is not here

Each repository's own version pins (httpd 2.4.68, Caddy 2.11.4, Composer, nginx modules) stay in that
repository's versions file. This manifest holds only what is shared across repositories.

## Changing a shared fact

1. Edit `images.yaml` and merge it.
2. In each repository run `--write`, review the diff and open a pull request. The `--check` job
   on that pull request is the proof that the repository agrees with the manifest again.

A namespace with `publish: false` (today `sitespanel`) appears in no README. Switching it to
`true` makes every repository's next `--write` list it.

## Known limits

* The php short description carries "PHP 8.2-8.5" as literal text. It moves to the per-repository
  versions file when that exists.
* Whether Docker Hub renders the HTML comments around a generated block invisibly has not been
  checked against a live Hub page.
