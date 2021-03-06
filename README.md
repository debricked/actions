# Github Actions for Debricked

This repository contains the source code for our Github Actions.

Remember that we also provide a Github integration as a [Github App](https://github.com/apps/debricked/), which can easily add dependency scans to your repositories without configuring actions.

You can always find documentation for our different ways of integrating with Debricked at https://debricked.com/documentation/.

## Usage

You can use the action `debricked/actions/scan@v1` to scan your repository.
The action needs two environmental variables: `USERNAME` and `PASSWORD`, to be set to your Debricked credentials.
You should store them in a secret variable, so they don't leak through the logs! See the example below.

This is an example workflow file:

```yaml
name: Vulnerability scan

on: [push, pull_request]

jobs:
  vulnerabilities-scan:
    runs-on: ubuntu-latest

    steps:
    - uses: actions/checkout@v2
    - uses: debricked/actions/scan@v1
      env:
        USERNAME: ${{ secrets.DEBRICKED_USERNAME }}
        PASSWORD: ${{ secrets.DEBRICKED_PASSWORD }}
```

Remember to add your username and password as secrets under Settings - Secrets in your repository.

### Skip scan feature

Sometimes you just wish to start a dependency scan in the background, without actually have it block the pipeline.
To do this, use the `skip-scan` action. It will upload dependency files to Debricked, without waiting for the scan results.
However, remember to visit Debricked regularly so you don't miss any new vulnerabilites in your code!

Example workflow with skip scan:

```yaml
name: Vulnerability scan

on: [push, pull_request]

jobs:
  vulnerabilities-scan:
    runs-on: ubuntu-latest

    steps:
    - uses: actions/checkout@v2
    - uses: debricked/actions/skip-scan@v1
      env:
        USERNAME: ${{ secrets.DEBRICKED_USERNAME }}
        PASSWORD: ${{ secrets.DEBRICKED_PASSWORD }}
```

### If you use languages that need a copy of the whole repository

In most cases, such as above, the tool only needs to upload your dependency files to the service.
However, for certain languages, you may need to upload a complete copy of the repository.
You then need to add the variable `UPLOAD_ALL_FILES: "true"` to the action, as below:

```yaml
name: Vulnerability scan

on: [push, pull_request]

jobs:
  vulnerabilities-scan:
    runs-on: ubuntu-latest

    steps:
    - uses: actions/checkout@v2
    - uses: debricked/actions/scan@v1
      env:
        USERNAME: ${{ secrets.DEBRICKED_USERNAME }}
        PASSWORD: ${{ secrets.DEBRICKED_PASSWORD }}
        UPLOAD_ALL_FILES: "true"
```

You can of course also combine this with skip scan action described above.
