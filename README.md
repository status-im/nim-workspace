# nim-workspace

A Nimble-based environment for working on one or more Status Nim projects.

## Prerequisites

Please install the standard C/C++ toolchain for your operating system
(e.g. `sudo apt install build-essentials`).

The scripts in this repo will automatically take care of installing the
right version of Nim and Nimble for you. To enable the automation, please
install `direnv` from your distro's package manager or by following the
instructions here:

https://github.com/direnv/direnv

### VScode

For VScode you have to install the following [extension](https://marketplace.visualstudio.com/items?itemName=nimsaem.nimvscode)

## Development workflows

### Configuring your IDE/editor

Once you have `direnv` installed you can fire up your editor from the terminal.
Note for the editors that have support for `direnv` you don't need to start the
editor from the terminal.

Here it is sample configuration for the `nim-codex` project `VScode`(it should
be placed in `nim-workspace/.vscode/settings.json`).

``` json
{
    "nim.provider": "lsp", // force using lsp over nimsuggest integration
    "nim.lintOnSave": false, // disable nim-check
    "nim.autoCheckProject": true, // you might want to set that to false for big projects. It will disable project compilation after saving a file.
    "nim.projectMapping": [{
        "projectFile": "nim-codex/codex.nim",
        "fileRegex": ".*\\.nim$" // force all files to be opened with once nimsuggest instance
    }],
    "nim.workingDirectoryMapping": [{
        "projectFile": "nim-codex/codex.nim",
        "directory": "nim-codex" // start nimsuggest for nim-codex/codex.nim in nim-codex folder
    }]
}
```

Where:

- `nim.provider: "lsp"` - force using lsp over nimsuggest integration
- `nim.lintOnSave: false` -  disable `nim check` in vscode
- `nim.autoCheckProject: true` - you might want to set that to `false` for big
  projects. It will disable project compilation after saving a file. In case
  this is disabled you may force that using `C-S-p Source Actions RET` and pick
  `Compile project`.
- `nim.projectMapping`: configure how `nimsuggest` instances can be started.
  The value in the sample section will force all nim files to be opened with the
  same `nimsuggest` instance.
- `nim.workingDirectoryMapping` - the mapping is used to determine which
  directory to start `nimsuggest`(typically it is the actual project root, but
  `nimlangserver` can't guess that).

_NB:_ make sure that you have imported the whole `nim-workspace` as a Workspace
folder in `VScode`. if you don't do that you have to adjust the values in the
settings file to strip the project name (i. e. `nim-codex`).

## Commands

### `add-project <project-name>`

Clones a Status project you intend to work on. An interactive script will
ask you whether you also want to clone each of the transitive dependencies
of the project. All dependencies that were not cloned for development will
be installed in the Nimble cache.

Projects can be removed from the workspace by just deleting the respective
directory. This will result in Nimble installing the project's package in
the global cache during the next `nimble build`.

### `add-all-vendor-projects`

This script must be executed within a repository using a `vendor` forder. It
will try to add all vendored submodules as folders in the workspace.

### `sync-vendor-revisions-to-workspace`

This script must be executed within a repository using a `vendor` folder. It
will copy the current revisions of submodules in the vendor folder to the
matching folder in the workspace if present. Typically, the script is executed
after pulling git revisions from other team members that have bumped vendor
sumbodules without also bumping the same packages in the Nimble lock file.
To resolve the arising discrepancy, the developer would execute the following
commands:

```bash
# Pull the project files as usual
cd top-level-project
git pull
make update # or git submodule update --init --recursive

# Fix the lock file
sync-vendor-revisions-to-workspace
nimble lock
git add nimble.lock
git commit -m "Update the lock file"
git push
```

### `sync-workspace-revisions-to-vendor`

This script must be executed within a repository using a `vendor` folder. It
will copy the revisions from the current workspace folders to the matching
submodules in the vendor folder. When you use a workspace and make changes
to the lockfile (by changing any of the dependencies) you must execute this
script before commiting to reflect the same change in the vendor folder, so
the respective project can continue building properly without Nimble.
