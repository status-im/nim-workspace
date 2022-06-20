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
be installed in the Nimble cache. `add-project` will clone the project from
`status-im` organization. If you want to clone project from different github
organization you may set ORG env variable.

Projects can be removed from the workspace by just deleting the respective
directory. This will result in Nimble installing the project's package in
the global cache during the next `nimble build`.
