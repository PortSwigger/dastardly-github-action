# Dastardly Scan Action

This action runs a Dastardly scan against a target site and creates a JUnit XML report for the scan on completion.

## Inputs

### `target-url`

**Required** The full URL of the site to scan.

### `output-filename`

**Optional** Where the output report file should be saved (in the `/github/workspace` directory).

**Default** `dastardly-report.xml`

## Examples
Below are some examples of how to use the action by running a Dastardly scan against our very own [Gin and Juice](https://ginandjuice.shop) site.

## Basic Usage
```
steps:
  - name: Run Dastardly Action Step
    uses: PortSwigger/dastardly-scan-action@v1
    with:
      target-url: 'https://ginandjuice.shop'
```

## Suggested Usage
By default, a workflow will fail if Dastardly finds any issue with a severity level of `MEDIUM` or `HIGH`, and produces a JUnit XML report of the scan. This may be fine for your
use case, but you may want to consider pairing this action with a JUnit XML parser that runs whether or not the Dastardly action step succeeded. This can be done by adding
`continue-on-error: true` to the Dastardly action step, or adding a condition for the parser to run regardless of success or failure.

```
steps:
  - name: Run Dastardly Action Step
    continue-on-error: true                        # This allows subsequent steps to run even if this step fails
    uses: PortSwigger/dastardly-scan-action@v1
    with:
      target-url: 'https://ginandjuice.shop'

  # You can replace this next step with any JUnit XML parser of your choosing
  - name: Publish Test Report
    if: success() || failure()                    # Forces this step to run on success or failure
    uses: mikepenz/action-junit-report@v3
    with:
      report_paths: '**/dastardly-report.xml'            # You would need to update this path if you passed in a different output filename to the Dastardly action
      require_tests: true
```

We suggest passing `require_tests: true` to this parser so that your workflow will fail if it could not find your specified output file.


Documentation for the parser used in this example can be found [here](https://github.com/marketplace/actions/junit-report-action).