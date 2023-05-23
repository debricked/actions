# Github Actions for Debricked

This repository contains the source code for our Github Actions.

Remember that we also provide a Github integration as a [Github App](https://github.com/apps/debricked/), which can easily add dependency scans to your repositories without configuring actions.

You can always find documentation for our different ways of integrating with Debricked at our [Debricked documentation](https://debricked.com/docs/integrations/ci-build-systems/github.html#github-actions).

## Usage

You can use the action `debricked/actions/scan@v2.0.0` to scan your repository.
The action needs one environmental variable: `DEBRICKED_TOKEN`, to be set to your Debricked API token.
You should store it in a secret variable, so it doesn't leak through the logs! See the example below.

This is an example workflow file:

```yaml
name: Vulnerability scan

on: [push]

jobs:
  vulnerabilities-scan:
    runs-on: ubuntu-latest

    steps:
    - uses: actions/checkout@v2
    - uses: debricked/actions/scan@v2.0.0
      env:
        DEBRICKED_TOKEN: ${{ secrets.DEBRICKED_TOKEN }}
```

Remember to add your debricked token as a secret under `Settings - Secrets` in your repository.

### Skip scan feature

Sometimes you just wish to start a dependency scan in the background, without actually have it block the pipeline.
To do this, use the `skip-scan` action. It will upload dependency files to Debricked, without waiting for the scan results.
However, remember to visit Debricked regularly so you don't miss any new vulnerabilites in your code!

Example workflow with skip scan:

```yaml
name: Vulnerability scan

on: [push]

jobs:
  vulnerabilities-scan:
    runs-on: ubuntu-latest

    steps:
    - uses: actions/checkout@v2
    - uses: debricked/actions/skip-scan@v2.0.0
      env:
        DEBRICKED_TOKEN: ${{ secrets.DEBRICKED_TOKEN }}
```

### If you use languages that need a copy of the whole repository

In most cases, such as above, the tool only needs to upload your dependency files to the service.
However, for certain languages, you may need to upload a complete copy of the repository.
You then need to add the variable `UPLOAD_ALL_FILES: "true"` to the action, as below:

```yaml
name: Vulnerability scan

on: [push]

jobs:
  vulnerabilities-scan:
    runs-on: ubuntu-latest

    steps:
    - uses: actions/checkout@v2
    - uses: debricked/actions/scan@v2.0.0
      env:
        DEBRICKED_TOKEN: ${{ secrets.DEBRICKED_TOKEN }}
        UPLOAD_ALL_FILES: "true"
```

You can of course also combine this with skip scan action described above.
