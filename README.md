# *chart-releaser* Action 2

A GitHub action to turn a GitHub project into a self-hosted Helm chart repo, using [helm/chart-releaser](https://github.com/helm/chart-releaser) CLI tool.

This is a fork of [helm/chart-releaser-action](https://github.com/helm/chart-releaser-action) with next updates:
1. Possibility to disable indexation, just create releases. 
1. Recursive scanning for charts starting from `charts_dir`.

## Usage

### Pre-requisites

1. A GitHub repo containing a directory with your Helm charts (eg: `/charts`)
1. A GitHub branch called `gh-pages` to store the published charts. See `charts_repo_url` for alternatives (Optional, see `no_index`)
1. Create a workflow `.yml` file in your `.github/workflows` directory. An [example workflow](#example-workflow) is available below. 
  For more information, reference the GitHub Help Documentation for [Creating a workflow file](https://help.github.com/en/articles/configuring-a-workflow#creating-a-workflow-file)

### Inputs

For more information on inputs, see the [API Documentation](https://developer.github.com/v3/repos/releases/#input)

- `version`: The chart-releaser version to use (default: v1.0.0)
- `charts_dir`: The charts directory
- `charts_repo_url`: The GitHub Pages URL to the charts repo (default: `https://<owner>.github.io/<project>`)
- `no_index`: Do not generate and do not upload index file to charts repo
- `scan`: Scan charts directories recursively starts from charts directory

### Example Workflow

Create a workflow (eg: `.github/workflows/release.yml`):

```yaml
name: Release Charts 2

on:
  push:
    branches:
      - master

jobs:
  release:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout
        uses: actions/checkout@v1

      - name: Configure Git
        run: |
          git config user.name "$GITHUB_ACTOR"
          git config user.email "$GITHUB_ACTOR@users.noreply.github.com"

      - name: Run chart-releaser
        uses: helm/chart-releaser-action@v1.0.0
        with:
          version: 'v1.0.0' #Optional
          charts_dir: '/charts' #Optional
          charts_repo_url: 'https://<owner>.github.io/<project>' #Optional
          no_index: false #Optional
          scan: false #Optional
        env:
          CR_TOKEN: "${{ secrets.GITHUB_TOKEN }}"
```

This uses [@helm/chart-releaser-action](https://www.github.com/helm/chart-releaser-action) to turn your GitHub project into a self-hosted Helm chart repo.
It does this – during every push to `master` – by checking each chart in your project, and whenever there's a new chart version, creates a corresponding [GitHub release](https://help.github.com/en/github/administering-a-repository/about-releases) named for the chart version, adds Helm chart artifacts to the release, and creates or updates an `index.yaml` file with metadata about those releases, which is then hosted on GitHub Pages

## Code of conduct

Participation in the Helm community is governed by the [Code of Conduct](CODE_OF_CONDUCT.md).
