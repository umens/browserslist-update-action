# browserslist-update-action

A GitHub Action that runs `npx update-browserslist-db@latest` on a repository and proposes a pull request to merge updates.

## v2 vs v1

`v1` uses command `npx browserslist@latest --update-db` which is now deprecated and to be removed in a future major `browserslist` release. It should be dropped in favor of `v2` which uses new command `npx update-browserslist-db@latest`.

:warning: v2 requires at least Node 15 (`npm` 6). Stick to `v1` otherwise.

## Usage

```yaml
name: Update Browserslist database

on:
  schedule:
    - cron: '0 2 1,15 * *'

permissions:
  contents: write
  pull-requests: write

jobs:
  update-browserslist-database:
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
      - name: Update Browserslist database and create PR if applies
        uses: c2corg/browserslist-update-action@v2
        with:
          github_token: ${{ secrets.GITHUB_TOKEN }}
          branch: browserslist-update
          base_branch: main
          commit_message: 'build: update Browserslist db'
          title: 'build: update Browserslist db'
          body: Auto-generated by [browserslist-update-action](https://github.com/c2corg/browserslist-update-action/)
          labels: 'chores, github action'
          reviewers: 'user1,user2,user3'
          teams: 'team1'
```

> [!IMPORTANT]  
> This action can only create a pull request if the *Allow GitHub Actions to create and approve pull requests* option is enabled in *Settings > Actions > General*. 

## Action inputs

Inputs with defaults are **optional**.

| Name           | Description                                                                                                                                                                                               | Default                                                                           |
| -------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | --------------------------------------------------------------------------------- |
| github_token   | `GITHUB_TOKEN` (`contents: write`, `pull-requests: write`) or a `repo` scoped [Personal Access Token (PAT)](https://docs.github.com/en/github/authenticating-to-github/creating-a-personal-access-token). | `GITHUB_TOKEN`                                                                    |
| branch         | The pull request branch name.                                                                                                                                                                             | `browserslist-update`                                                             |
| base_branch    | The target branch into which the pull request will be merged.                                                                                                                                             | `master`                                                                          |
| directory      | For monorepos, the directory to switch to when running action                                                                                                                                             | `.`                                                                               |
| commit_message | The message to use when committing changes.                                                                                                                                                               | `Update caniuse database`                                                         |
| title          | The title of the pull request.                                                                                                                                                                            | `📈 Update caniuse database`                                                      |
| body           | The body of the pull request.                                                                                                                                                                             | `Caniuse database has been updated. Review changes, merge this PR and have a 🍺.` |
| labels         | A comma separated list of labels to apply to the pull request.                                                                                                                                            | (no label)                                                                        |
| reviewers      | A comma separated list of users to be added to the the pull request reviewers list.                                                                                                                                                                           | (no users) |
| teams          | A comma separated list of teams to be added to the the pull request reviewers list.                                                                                                                                                                          | (no teams) |

One will usually run this action on a cron basis (say, every day or week)

## Action outputs

| Name      | Description                                                                                        |
| --------- | -------------------------------------------------------------------------------------------------- |
| has_pr    | `true` if changes were found and a pull request was created or updated, `false` otherwise.         |
| pr_number | The number of the pull requested created or updated, if applies.                                   |
| pr_status | Can be either `created` or `updated` depending on whether the pull request was created or updated. |

## Contributing

### Edit / add GraphQL queries and mutations

`src/generated` folder contains generated type definitions based on queries. Run `npm run graphql` to update.

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

Keep a major version tag synchronized with updates, e.g. if you publish version `v2.0.3`, then a `v2` branch should be positioned at the same location.
