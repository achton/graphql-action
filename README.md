# Octokit Request Action

> A GitHub Action to send queries to GitHub's GraphQL API

[![Build Status](https://github.com/octokit/graphql-action/workflows/Test/badge.svg)](https://github.com/octokit/graphql-action/actions)
[![Greenkeeper](https://badges.greenkeeper.io/octokit/graphql-action.svg)](https://greenkeeper.io/)

## Usage

Minimal example

```yml
name: Log latest release
on:
  push:
    branches:
      - master

jobs:
  logLatestRelease:
    runs-on: ubuntu-latest
    steps:
      - uses: octokit/graphql-action@v1.x
        id: get_latest_release
        with:
          query: |
            query release($owner:String!,$repo:String!) {
              repository(owner:$owner,name:$repo) {
                releases(first:1) {
                  nodes {
                    name
                    createdAt
                    tagName
                    description
                  }
                }
              }
            }
        env:
          GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}
      - run: "echo 'latest release: ${{ steps.get_latest_release.outputs.data }}'"
```

To access deep values of `outputs.data`, check out [`gr2m/get-json-paths-action`](https://github.com/gr2m/get-json-paths-action).

## Debugging

To see additional debug logs, create a secret with the name: `ACTIONS_STEP_DEBUG` and value `true`.

## How it works

`octokit/graphql-action` is using [`@octokit/graphql`](https://github.com/octokit/graphql.js/) internally with some additions

1. Requests are authenticated using the `GITHUB_TOKEN` environment variable. It is required to prevent rate limiting, as all anonymous requsets from the same origin count against the same low rate.
2. The `owner` and `repo` parameters are preset to the repository that the action is run in.

The actions sets `data` output to the response data. Note that it is a string, you cannot access any keys of the response at this point. The action also sets `headers` (again, to a JSON string) and `status`.

## License

[MIT](LICENSE)
