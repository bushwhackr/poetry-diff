# `bushwhackr/poetry-diff`

---

This action will diff the poetry.lock/pyproject.toml file between your PR and its base branch. It will output the 
difference in the form of a markdown table. This is useful for tracking changes in dependencies between PR's.

## Sample Output

| Package         | Base   | Head        |
|-----------------|--------|-------------|
| tzdata          | (null) | 2024.1      |
| numpy           | (null) | 1.26.4      |
| pandas          | (null) | 2.2.2       |
| pytz            | (null) | 2024.1      |
| python-dateutil | (null) | 2.9.0.post0 |
| six             | (null) | 1.16.0      |

## Usage

```yaml
## Prerequisites
- uses: actions/checkout@v4
  with:
    fetch-depth: 0  # This is required to fetch older pyproject.toml and poetry.lock files.
- name: Set up Python
  uses: actions/setup-python@v5
  with:
    python-version: 3.10
- uses: abatilo/actions-poetry@v3
  with:
    poetry-version: 1.8.2

## bushwhackr/poetry-diff
- uses: bushwhackr/poetry-diff@v1
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



---

## Full Sample Workflow

Refer to: [sample.yaml](docs/sample.yaml)

```yaml
name: Dependency Changes
on:
  pull_request: {}
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
          python-version: "3.10"
      - uses: abatilo/actions-poetry@v3
        with:
          poetry-version: "1.8.2"
      - uses: bushwhackr/poetry-diff@v1
        id: poetry-diff
      - uses: mshick/add-pr-comment@v2
        if: ${{ steps.poetry-diff.outputs.diff_present != '0' }}
        with:
          message: |-
            ${{ steps.poetry-diff.outputs.diff_content }}
```
