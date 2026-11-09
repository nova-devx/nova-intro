# v1.0.0 tag & release — nova-intro

Run from the nova-intro repo root, in this order (commit/push first, tag second, tag-push last):

```bash
# 1. Commit + push the updated README and code
git add .
git commit -m "Release v1.0.0"
git push origin main

# 2. Create the annotated tag
git tag -a v1.0.0 -m "Release v1.0.0"

# 3. Push the tag (this is what triggers the release workflow)
git push origin v1.0.0

# 4. Confirm the release workflow fired
gh run list --repo nova-devx/nova-intro
```

The release workflow lives in `.github/workflows/release.yml`. It fires on any push of a tag matching `v[0-9]+.[0-9]+.[0-9]+` (e.g. `v1.0.0`), packages the source as a tarball, and publishes it as a GitHub Release with auto-generated notes.

## Gotchas
- **Tag must be numeric-format** — use `v1.0.0` exactly. Non-numeric or malformed tag names (e.g. `v1.0`, `1.0.0rc1`) won't match the release workflow's trigger pattern.
- **The push must include the tag** — `git push origin main` alone does NOT trigger the workflow. The tag only exists locally until `git push origin v1.0.0` (or `git push --tags`) sends it; the Actions workflow fires on the tag push, not the commit push.

## Order matters
- Tag before commit/push: the tag points at the old commit — wrong.
- Push tag before commit/push: the workflow runs against code that isn't on the remote yet — wrong.
- Commit/push → tag → push tag is the only order that works.

## If the workflow didn't fire
```bash
gh run list --repo nova-devx/nova-intro --limit 5
gh run view <RUN_ID> --repo nova-devx/nova-intro
```
Check the tag actually landed: `git ls-remote --tags origin` should show `v1.0.0`.

## Done when
- `gh run list` shows a new run for the release workflow (status: success)
- The GitHub Releases page shows v1.0.0 with the generated assets
