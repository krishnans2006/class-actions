# actions

GitHub Actions used to manage class repositories.

## How it works

- A commit is made to the `main` branch of a class repository, triggering:
  - [class/update-hub.yml](https://github.com/krishnans2006-classes/template/blob/main/.github/workflows/update-hub.yml) `trigger-hub-update`
  - [actions/dispatch-hub-update.yml](https://github.com/krishnans2006-classes/actions/blob/main/.github/workflows/dispatch-hub-update.yml) `dispatch-hub-update`
  - [repository-dispatch@v2](https://github.com/peter-evans/repository-dispatch) to send a repository dispatch event to the hub
- The hub picks up the repository dispatch event and runs:
  - [hub/update-hub.yml](https://github.com/krishnans2006-classes/hub/blob/main/.github/workflows/update-hub.yml) `handle-hub-update`
  - [actions/do-hub-update.yml](https://github.com/krishnans2006-classes/actions/blob/main/.github/workflows/do-hub-update.yml) `do-hub-update`
  - [checkout@v4](https://github.com/actions/checkout) to checkout the hub repository
  - Update all class repositories
  - Commit and push submodule changes to the hub repository

## Issues to solve

### Slow actions

Cloning the hub repository and every submodule takes a really long time (~1min), and will only get worse as more classes and materials are added.

Updating every submodule is also slow, but not nearly as slow as cloning.

Cloning and updating specific submodules would be ideal and should be further looked into.

### Multiple class repository pushes

Since these actions take minutes to run, and since every submodule is updated, edge cases are abundant.

For example, if multiple class repositories are pushed to in a short period of time:
- The first action will update both class repositories, despite being triggered by just one
- The second action will fail, since no class repositories needed updating anymore

The exact error is:
```
On branch main
Your branch is up to date with 'origin/main'.

nothing to commit, working tree clean
Error: Process completed with exit code 1.
```
