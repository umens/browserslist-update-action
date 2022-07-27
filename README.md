# browserslist-update-action

A Github Action that runs `npx browserslist@latest --update-db` on a repository and proposes a pull request to merge updates.

## Usage

```yaml
name: Update caniuse database

on:
  schedule:
    - cron: '0 2 1,15 * *'

jobs:
  update-caniuse-database:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout repository
        uses: actions/checkout@v3
        with:
          fetch-depth: 0
      - name: Configure git
        run: |
          git config --global user.email "action@github.com"
          git config --global user.name "GitHub Action"
      - name: Update caniuse database and create PR if applies
        uses: c2corg/browserslist-update-action@v1
        with:
          github_token: ${{ secrets.GITHUB_TOKEN }}
          branch: browserslist-update
          commit_message: 'build: update browserslist db'
          title: 'build: update browserslist db'
          body: Auto-generated by [browserslist-update-action](https://github.com/c2corg/browserslist-update-action/)
```

## Action inputs

Inputs with defaults are **optional**.

| Name           | Description                                                                                                                                                                                               | Default                                                                           |
| -------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | --------------------------------------------------------------------------------- |
| github_token   | `GITHUB_TOKEN` (`contents: write`, `pull-requests: write`) or a `repo` scoped [Personal Access Token (PAT)](https://docs.github.com/en/github/authenticating-to-github/creating-a-personal-access-token). | `GITHUB_TOKEN`                                                                    |
| branch         | The pull request branch name.                                                                                                                                                                             | `browserslist-update`                                                             |
| directory      | For monorepos, the directory to switch to when running action                                                                                                                                             | `.`                                                                               |
| commit_message | The message to use when committing changes.                                                                                                                                                               | `Update caniuse database`                                                         |
| title          | The title of the pull request.                                                                                                                                                                            | `📈 Update caniuse database`                                                      |
| body           | The body of the pull request.                                                                                                                                                                             | `Caniuse database has been updated. Review changes, merge this PR and have a 🍺.` |

One will usually run this action on a cron basis (say, every day or week)

## Contributing

### Edit / add GraphQL queries and mutations

`src/types` folder contains generated type definitions based on queries. Run `npm run types` to update.

### Release a version

```sh
npm run lint
npm run build
npm run pack
```

Or shortly

```sh
npm run all
```

Then bump version number in `package.json` and `package-lock.json`. Push commits.

Keep a major version tag synchronized with updates, e.g. if you publish version `v2.0.3`, then a `v2` tag should be positioned at the same location.
