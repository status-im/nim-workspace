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
the project can continue building properly without Nimble.
