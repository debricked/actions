# GitHub Actions for Debricked

This repository contains the source code for our GitHub Actions.

Remember that we also provide a GitHub integration as a [GitHub App](https://github.com/apps/debricked/), which is used to create automatic [Pull Requests with root fixes](https://portal.debricked.com/vulnerability-management-43/debricked-s-pull-requests-201).

You can always find documentation for our different ways of integrating with Debricked at our [Debricked documentation](https://debricked.com/docs/integrations/ci-build-systems/github.html#github-actions).

## Usage

### Scan

You can use the action `debricked/actions@v3.0.2` to scan your repository.
The action needs one environmental variable: `DEBRICKED_TOKEN`, to be set to your Debricked API token.
You should store it in a secret variable under `Settings - Secrets` in your repository, so it doesn't leak through the logs!

This is an example workflow file which uses our Docker image:

```yaml
name: Vulnerability scan

on: [push]

jobs:
  vulnerabilities-scan:
    runs-on: ubuntu-latest

    steps:
      - uses: actions/checkout@v3
      - uses: debricked/actions@v3.0.2
        env:
          DEBRICKED_TOKEN: ${{ secrets.DEBRICKED_TOKEN }}
```

But it is also possible to run it standalone, making it possible for you to customise the runtime environment:

```yaml
name: Vulnerability scan

on: [push]

jobs:
  vulnerabilities-scan:
    runs-on: ubuntu-latest

    steps:
      - uses: actions/checkout@v3
      - uses: debricked/actions/cache@v3.0.2
      - uses: debricked/actions/scan-non-docker@v3.0.2
        env:
          DEBRICKED_TOKEN: ${{ secrets.DEBRICKED_TOKEN }}
```

When scanning, the High Performance resolution is enabled by default but can be disabled using the `--no-resolve` flag

`scan` command also supports a number of different flags which will help you to adjust scan behavior to your needs. You can find out more about them on [Debricked Portal](https://portal.debricked.com/debricked-cli-63/debricked-cli-documentation-298?postid=472#scan)

### Resolve

This command analyses your project to find eligible manifest files, that do not have related lock files, and uses them to generate the appropriate Debricked lock files.

Example workflow using our Docker image:

```yaml
name: Debricked resolve

on: [push]

jobs:
  resolve:
    runs-on: ubuntu-latest

    steps:
      - uses: actions/checkout@v3
      - uses: debricked/actions/resolve@v3.0.2
        env:
          DEBRICKED_TOKEN: ${{ secrets.DEBRICKED_TOKEN }}
```

Example workflow without Docker:

```yaml
name: Debricked resolve

on: [push]

jobs:
  resolve:
    runs-on: ubuntu-latest

    steps:
      - uses: actions/checkout@v3
      - uses: debricked/actions/cache@v3.0.2
      - uses: debricked/actions/resolve-non-docker@v3.0.2
        env:
          DEBRICKED_TOKEN: ${{ secrets.DEBRICKED_TOKEN }}
```

You can read more about `resolve` command on [High Performance Scan: faster, more accurate, and more secure dependency scanning](https://portal.debricked.com/debricked-cli-63/high-performance-scan-faster-more-accurate-and-more-secure-dependency-scanning-293) page

And you can find out more about flags supported by `resolve` command on [Debricked Portal](https://portal.debricked.com/debricked-cli-63/debricked-cli-documentation-298?postid=472#resolve) 