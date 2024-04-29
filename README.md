# `bushwhackr/poetry-diff`

This action will diff the poetry.lock file between your PR and its base branch.

## Usage

```yaml
## Prerequisites
- uses: actions/checkout@v4
  with:
    fetch-depth: 0
- name: Set up Python
  uses: actions/setup-python@v5
  with:
    python-version: 3.10
- uses: abatilo/actions-poetry@v3
  if: ${{ matrix.poetry-version != 'null' }}
  with:
    poetry-version: 1.8.2

## bushwhackr/poetry-diff
- uses: bushwhackr/poetry-diff
  id: poetry-diff
  with:
    # This is optional. If not provided, the action will use the default value of '.' (current directory).
    path: <path to poetry.lock directory>
    
## Output (Displaying the diff)
## NOTE: This requires on setting certain write permissions to the PR.
##     : @see https://github.com/mshick/add-pr-comment
- uses: mshick/add-pr-comment@v2
  if: ${{ steps.poetry-diff.outputs.diff_present != '0' }}
  with:
    message: |-
      ${{ steps.poetry-diff.outputs.diff_content }}
```

## Sample Workflow

```yaml
name: Dependency Changes
on:
  pull_request: { }
jobs:
  test:
    runs-on: ubuntu-latest
    permissions:
      pull-requests: write
      contents: read
    steps:
      - uses: actions/checkout@v4
        with:
          fetch-depth: 0
      - name: Set up Python
        uses: actions/setup-python@v5
        with:
          python-version: 3.10
      - uses: abatilo/actions-poetry@v3
        with:
          poetry-version: 1.8.2
      - uses: bushwhackr/poetry-diff
        id: poetry-diff
      - uses: mshick/add-pr-comment@v2
        if: ${{ steps.poetry-diff.outputs.diff_present != '0' }}
        with:
          message: |-
            ${{ steps.poetry-diff.outputs.diff_content }}
```
