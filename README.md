# Class Actions

GitHub Actions used to manage class repositories.

## How it works

- A commit is made to the `main` branch of a class repository, triggering:
  - [class/update-submodule.yml](https://github.com/krishnans2006/class-template/blob/main/.github/workflows/update-submodule.yml) `trigger-submodule-update`
  - [actions/dispatch-submodule-update.yml](https://github.com/krishnans2006/class-actions/blob/main/.github/workflows/dispatch-submodule-update.yml) `dispatch-submodule-update`
  - [repository-dispatch@v2](https://github.com/peter-evans/repository-dispatch) to send a repository dispatch event to the main classes repository.
- The main repo picks up the repository dispatch event and runs:
  - [classes/update-submodules.yml](https://github.com/krishnans2006/classes/blob/main/.github/workflows/update-submodules.yml) `handle-submodule-update`
  - [actions/do-submodule-update.yml](https://github.com/krishnans2006/class-actions/blob/main/.github/workflows/do-submodule-update.yml) `do-submodule-update`
  - [checkout@v4](https://github.com/actions/checkout) to checkout the hub repository
  - [Commands](https://github.com/krishnans2006/class-actions/blob/main/.github/workflows/do-submodule-update.yml#L26-L39) to update the submodule SHA, commit, and push

## Issues to solve

There are currently no more issues left to solve. The below issues have been solved by [eb99134](https://github.com/krishnans2006/class-actions/commit/eb9913466836a5054798c37c8bfc788886f9cfb0).

---

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
