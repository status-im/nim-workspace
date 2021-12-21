# nim-workspace

A Nimble-based environment for working on one or more Status Nim projects.

## Pre-requisites

The scripts in this repo will automatically manage your Nim and Nimble versions.
To enable the automation, please install `direnv` from your distro's package
manager or by following the instructions here:

https://github.com/direnv/direnv

## Commands

### `./add-project <project-name>`

Clones a Status project you intend to work on. An interactive script will
ask you whether you also want to clone each of the transitive dependencies
of the project. All dependencies that were not cloned for development will
be installed in the Nimble cache.

Projects can be removed from the workspace by just deleting the respective
directory. This will result in Nimble installing the project's package in
the global cache during the next `nimble build`.
