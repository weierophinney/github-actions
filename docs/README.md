# Laminas GitHub Actions: docs

This action can be used to build and deploy documentation for a Laminas
or Mezzio repository.

The action makes several assumptions:

- You want to use the [laminas/documentation-theme](https://github.com/laminas/documentation-theme).
- You will build and deploy your docs in the gh-pages branch.
- You will build your docs in the `docs/html` directory (though this is configurable)

If no `mkdocs.yml` file is available in the repository, the action aborts early
with no errors.

As an example:

```yaml
- name: Docs
  uses: laminas/github-actions/docs@master
  with:
    emptyCommits: false
    publishDir: doc/html
    siteUrl: https://api-tools.laminas.dev
```

The above will build docs in the `doc/html` subdirectory, will NOT build for
empty commits, and uses an alternate base site URL.

## Behaviors

- By default, the action auto-determines the `siteUrl` based on the repository,
  matching against the repository organization.

- By default, it will build even when the commit is empty.

## Options

- emptyCommits: Specify whether to build documentation for empty commits.
  Defaults to `true`.

- username: Specify a git username under which to make the documentation commit.
  If none is specified, it derives it from the user who made the commit that
  triggered the action.

- useremail Specify a git user email under which to make the documentation commit.
  If none is specified, it uses `${username}@users.noreply.github.com`.

- publishDir: Specify the directory under the `${GITHUB_WORKSPACE}` in which to
  clone the gh-pages branch and build documentation. By default, it assumes
  `docs/html`.

- siteUrl: Specify the scheme and authority of the site URL under which
  documentation will be available. By default, this will be either
  https://docs.laminas.dev or https://docs.mezzio.dev as derived from the
  `${GITHUB_REPOSITORY}` value.

## Environment

This action requires the environment variable `$ACTIONS_DEPLOY_KEY`, which
should be a deployment key.

Generate a deployment key as follows:

```bash
$ ssh-keygen -t rsa -b 4096 -C "${git config user.email}" -f gh-pages -N ""
# Creates the files:
# - gh-pages (private SSH key)
# - gh-pages.pub (public key)
```

Go to the **Settings** tab of your repository.

Under **Deploy Keys**, select the **Add Key** button, add the contents of your
`gh-pages.pub` file, and select the "Allow Write Access" checkbox.

Then go to **Secrets**, select the **Add** button, give the new secret the title
`ACTIONS_DEPLOY_KEY`, and paste in the contents of your `gh-pages` file.

## Example workflow

```yaml
name: docs

on:
  push:
    branches:
      - master
  repository_dispatch:

jobs:
  build-deploy:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v2
      - name: Build and deploy documentation
        uses: laminas/github-actions/docs@master
        with:
          emptyCommits: false
```
