# Pants Example

A Python monorepo built with [Pants](https://www.pantsbuild.org). Shared libraries and several services live in one repo, and Pants builds each service as a PEX file that holds only the code and the packages it imports.

## What it shows

In a monorepo, each service needs a different part of the code. Pants reads the imports of every file and works out the dependency graph, so a `BUILD` file names its targets and seldom lists its dependencies.

| Target | Uses | Shows |
| --- | --- | --- |
| `srvs/srv1` | nothing | A PEX binary and a Docker image built from it |
| `srvs/srv2` | `libs/lib1` | A service that imports a first-party library |
| `srvs/srv3` | `libs/lib2` | A library with a third-party package, `numpy`, declared in its `BUILD` file |
| `srvs/srv4` | its own `pkg` package | A service with internal modules |
| `srvs/srv5` | `libs/lib3` | A library whose packages come from a `requirements.txt` |
| `srvs/jango` | `django` | A Django project, run through a PEX that Pants builds |

Each PEX holds only what its service imports. So `srv2` ships without `numpy`, and `srv5` ships without `django`.

## Run it

1. Install [mise](https://mise.jdx.dev/getting-started.html).
2. Install the tools. The first `pants` call downloads the Pants version set in `pants.toml`:

   ```bash
   git clone git@github.com:tabmadi/pants-example.git
   cd pants-example
   mise install
   mise run setup
   ```

3. Run the services:

   ```bash
   pants run srvs/srv2:run
   pants run srvs/srv3:run
   pants run srvs/srv5:run
   pants run srvs/jango:manage -- check
   ```

4. Package every binary into `dist/`, then look at one:

   ```bash
   mise run build
   unzip -l dist/srvs.srv2/run.pex | grep -E 'libs|numpy'
   ```

`mise run image` builds the Docker image of `srv1`. It needs [Docker](https://docs.docker.com/get-docker/).

## How it works

| File | Holds |
| --- | --- |
| [`pants.toml`](pants.toml) | The Pants version, the enabled backends, and the Python version for the code |
| `BUILD` in each directory | The targets: `python_sources`, `pex_binary`, `python_requirement`, and `docker_image` |
| [`libs/lib3/requirements.txt`](libs/lib3/requirements.txt) | The packages of `lib3`, read by `python_requirements` |
| [`srvs/srv1/Dockerfile`](srvs/srv1/Dockerfile) | The image of `srv1`. Pants copies the PEX into the build context |

`pants list ::` shows every target, and `pants dependencies srvs/srv3:run` shows what one target pulls in.

## What is deliberately missing

- **Lockfiles**: the packages are pinned in the `BUILD` files and `requirements.txt`, without a Pants resolve. A real monorepo turns on resolves and commits the lockfile.
- **Tests**: no `python_tests` target exists. The Django test file is the empty one that `startapp` writes.
- **Type checks**: Pants supports mypy and Pyright as backends. They are not enabled.

## Tasks

| Task | Does |
| --- | --- |
| `mise run setup` | Downloads Pants and installs the git hooks |
| `mise run list` | Lists every target |
| `mise run build` | Packages every PEX binary into `dist/` |
| `mise run image` | Builds the Docker image of `srv1` |
| `mise run lint` | Runs Black and hadolint through Pants, then `rumdl` and `gitleaks` |
| `mise run format` | Applies every formatter |
| `mise run test` | Runs every test target |
| `mise run check` | Runs `lint` and `test`. Run it before you finish a change |
| `mise run ci` | Runs exactly what CI runs. The `pre-push` hook runs it too |

## License

Apache License 2.0. See [LICENSE](LICENSE).
