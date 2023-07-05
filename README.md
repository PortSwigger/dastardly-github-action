# Dastardly Scan Action

This action runs a Dastardly vulnerability scan against a target site. On completion, a JUnit XML report is generated containing information about the vulnerabilities found, where
they were located, additional information about the vulnerability and links to our learning resources with suggestions on how to fix them.

## About Dastardly

- [Dastardly](https://portswigger.net/burp/dastardly) is a free, lightweight web application security scanner for your CI/CD pipeline.
- It is designed specifically for web developers, and checks your application for [seven security issues](https://portswigger.net/burp/dastardly/scan-checks) that are likely to interest you during software development.
- Dastardly is based on the same scanner as [Burp Suite](https://portswigger.net/burp) (Burp Scanner).

For full documentation on using Dastardly, please consult the [Dastardly documentation](https://portswigger.net/burp/documentation/dastardly).

Already used Dastardly? [Tell us what you think here](https://forms.gle/8Va7ombB793HqFKw5).

## Inputs

## `target-url`

**Required** The full URL (including scheme) of the site to scan.

## `output-filename`

**Optional** The name of the output report file. This will be stored in the GITHUB_WORKSPACE (/github/workspace) directory.

**Default** `dastardly-report.xml`

## Examples
Below are some examples of how to use the action by running a Dastardly scan against our very own [Gin and Juice Shop](https://ginandjuice.shop) site. This is a deliberately
vulnerable web application designed for testing web vulnerability scanners.

## Basic Usage
```
steps:
  - name: Run Dastardly Action Step
    uses: PortSwigger/dastardly-github-action@main
    with:
      target-url: 'https://ginandjuice.shop'
```

## Suggested Usage
Dastardly produces a JUnit XML report of the scan on completion. This report will only include vulnerability details if vulnerabilities were found by the scanner.

By default, if Dastardly finds any issue with a severity level of `LOW`, `MEDIUM`, or `HIGH`, it will fail a workflow build.

This may be fine for your use case, but you may want to consider pairing this action with a JUnit XML parser that runs whether or not the Dastardly action step succeeded. You can
do this by adding `continue-on-error: true` to the Dastardly action step, or adding a condition for the parser to run regardless of success or failure.

```
steps:
  - name: Run Dastardly Action Step
    continue-on-error: true                        # This allows subsequent steps to run even if this step fails
    uses: PortSwigger/dastardly-github-action@main
    with:
      target-url: 'https://ginandjuice.shop'

  # You can replace this next step with any JUnit XML parser of your choosing
  - name: Publish Test Report
    if: always()                                    # Forces this step to always run
    uses: mikepenz/action-junit-report@v3
    with:
      report_paths: '**/dastardly-report.xml'       # You need to update this path if you pass in a different output filename to the Dastardly action
      require_tests: true
```

We suggest passing `require_tests: true` to this parser so that your workflow will fail if it could not find your specified output file.


Documentation for the parser used in this example can be found [here](https://github.com/marketplace/actions/junit-report-action).
