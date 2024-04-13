# Nix Stores Explorer

The project goal is to build a service for exploring Nix stores ([link](https://nixos.org/)).

## Technical Stack

- Python 3.11
- Poetry as an environment and module management
- FastAPI for the REST
- OpenAPI for REST docs
- SQLite for persistency
- Streamlit for the front
- GitHub for repo and CI
- Nix for dependency management

### Functionality

A user should be able to:

- Register
- Log in
- Log out
- Perform tasks with Nix stores:
  - Create a store with a given name
    - A store name exists only for a user
    - A store is located in a directory ([link](https://nixos.org/manual/nix/unstable/command-ref/new-cli/nix3-path-info))
  - Get a list of existing stores
  - Remove a store with a given name
  - Add a package to a store
  - Remove a package from a store
  - Get the difference of sets of paths in two given stores
  - Get the difference of closures of two given packages
    - [1](https://zero-to-nix.com/concepts/closures), [2](https://nixos.org/manual/nix/unstable/command-ref/new-cli/nix3-path-info), [3](https://nixos.org/manual/nix/unstable/command-ref/new-cli/nix3-derivation-show)
    - Given `nixpkgs#bash` and `nixpkgs#hello`, find the paths that are dependencies of `nixpkgs#bash` (including its `out`), but aren't dependencies of `nixpkgs#hello`
    - Packages can be in different stores
  - Get the size (in bytes) of a package closure
  - Check whether a package is in the store

## Front end

This is a minimum:

- Input field for a JSON request
- A button to submit the request
- A field to pretty-print the server response

## Deployment

The service should be runnable on a Linux machine.

## GitHub Actions

Suggested actions:

- [nixbuild/nix-quick-install-action](https://github.com/nixbuild/nix-quick-install-action)
- [nix-community/cache-nix-action](https://github.com/nix-community/cache-nix-action)

## Nix Stores

Note that performing tasks with Nix stores does not go beyond:

- Querying an SQLite database located in `/nix/var/nix/db/db.sqlite` of a store
- Running [Nix commands](https://nixos.org/manual/nix/stable/command-ref/new-cli/nix)
- Keeping track of associations between users and their stores in an SQLite database

## Nix Flake

Provide a basic [Nix flake](https://nixos.wiki/wiki/Flakes) using [flake-utils](https://github.com/numtide/flake-utils) or [flake-parts](https://flake.parts/).

## Scripts

In the flake, provide scripts for testing your application according to the [Quality Requirements](#quality-requirements).

Scripts should be defined in Nix via [writeShellApplication](https://nixos.org/manual/nixpkgs/unstable/#trivial-builder-writeShellApplication).

## Quality Requirements

Quality requirements are expressed as quality attribute scenarios.

Quality characteristics source - [ISO/IEC 25010](https://iso25000.com/index.php/en/iso-25000-standards/iso-25010).

### Maintainability

#### Testability

- **Source:** developer
- **Stimulus:** developer creates a new function
- **Environment:** normal development
- **Artifact:** a module
- **Response:** developer adds unit tests (doctests or anything else)
- **Response measure:**
  - [Coverage](https://www.atlassian.com/continuous-delivery/software-testing/code-coverage)
  - Statement coverage is over 60%
  - Branch coverage is over 60%
  - Function coverage is over 60%

### Reliability

#### Recoverability

- **Source:** developer
- **Stimulus:** developer stops the server
- **Environment:** normal operation
- **Artifact:** each store
- **Response:** store database agrees with the store contents
- **Response measure:**
  - 100% of ValidPaths listed in the store database are in the store.

### Performance Efficiency

#### Time Behavior

- **Source:** users
- **Stimulus:** 10 requests per second for 1 minute
- **Environment:** normal operation
- **Artifact:** server
- **Response:** server response
- **Response measure:**
  - Server responds to each request within 1 second

### Security

#### Confidentiality

- **Source:** user
- **Stimulus:** registers with a name and a password
- **Environment:** normal operation
- **Artifact:** the server
- **Response:** server stores encrypted password in the database
- **Response measure:**
  - The password is successfully checked using bcrypt within 1 second
