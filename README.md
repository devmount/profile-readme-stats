# Profile Readme Stats

Showcase your github stats on your profile README.md.

This action provides [template strings](#template-strings) that are replaced with their respective values when the action runs.

**Example:** [TEMPLATE](/raw/refs/heads/main/TEMPLATE.md) → [README](./OUTPUT.md)

## Table of contents

- [Table of contents](#table-of-contents)
- [Action Inputs](#action-inputs)
- [Template Strings](#template-strings)
  - [General](#general)
  - [Languages](#languages)
  - [Extra Options](#extra-options)
    - [`uri`](#uri)
    - [`max`](#max)
- [Example Workflow](#example-workflow)

## Action Inputs

| Input | Required | Default | Description |
| --- | --- | --- | --- |
| `token` | Yes | – | Personal access token with `read:user` scope and optional `repo` scope. Generate under [settings/tokens](https://github.com/settings/tokens). **Note:** `repo` scope is needed for taking private repositories into account. |
| `template` | No | `./TEMPLATE.md` | Path to template |
| `readme` | No | `./README.md` | Path to generated file |
| `includeForks` | No | `false` | Include forked repositories when calculating the stats |

## Template Strings

The following marker can be used in the template file to insert queried data.

### General

| Template String | Description |
| --- | --- |
| `{{ ACCOUNT_AGE }}` | Account age in years. |
| `{{ ISSUES }}` | Total number of opened issues across all repositories. |
| `{{ PULL_REQUESTS }}` | Total number of opened pull requests across all repositories. |
| `{{ CODE_REVIEWS }}` | Total number of pull requests reviewed across all repositories. |
| `{{ COMMITS }}` | Total number of commits across all repositories. Includes commits in private repositories only if you allowed github to show your private contributions on your profile (check out the [docs](https://docs.github.com/en/github/setting-up-and-managing-your-github-profile/publicizing-or-hiding-your-private-contributions-on-your-profile#changing-the-visibility-of-your-private-contributions) for more info). |
| `{{ GISTS }}` | Total number of public gists. |
| `{{ REPOSITORIES }}` | Total number of repositories. Includes private repositories if the given personal access token has `repo` scope (see more Action Inputs > token above). |
| `{{ REPOSITORIES_CONTRIBUTED_TO }}` | Total number of repositories you contributed to. |
| `{{ STARS }}` | Total number of stars on all your gists and repositories. |

### Languages

A region that will be repeated for every language you use in your repositories.

| Template String | Description |
| --- | --- |
| `{{ LANGUAGE_TEMPLATE_START }}` | Special template string that signifies the start of the region. |
| `{{ LANGUAGE_NAME }}` | Name of the language. |
| `{{ LANGUAGE_PERCENT }}` | How often the language is used in your repositories (percentage wise). |
| `{{ LANGUAGE_COLOR }}` | Color of the language (in CSS color format ex: `#0248AC`). |
| `{{ LANGUAGE_TEMPLATE_END }}` | Special template string that signifies the end of the region. |

### Extra Options

#### `uri`

Will encode the value as an URI component

**Example:**

```twig
{{ LANGUAGE_COLOR:uri }}
```

#### `max`

Can only be used with `LANGUAGE_TEMPLATE_START`

Will run the inner template at most `max` number of times

**Example:**

```twig
{{ LANGUAGE_TEMPLATE_START:max=5 }}
This text will be printed at most 5 times
{{ LANGUAGE_TEMPLATE_END }}
```

## Example Workflow

```yml
on:
  schedule:
    - cron: '0 */12 * * *' # every 12 hours
  push:
    branches:
      - master
      - main
jobs:
  publish:
    runs-on: ubuntu-latest
    steps:
    - uses: actions/checkout@v6
      with:
        fetch-depth: 0
    - name: Generate README.md
      uses: devmount/profile-readme-stats@v1
      with:
        token: ${{ secrets.USER_TOKEN }}
    - name: Update README.md
      run: |
        if [[ "$(git status --porcelain)" != "" ]]; then
        git config user.name github-actions[bot]
        git config user.email 41898282+github-actions[bot]@users.noreply.github.com
        git add .
        git commit -m "Update README"
        git push
        fi
```
