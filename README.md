# Read-ME


# This step listens for the learner to merge a pull request with branch `my-first-branch`.
# This workflow updates from step 4 to step X.

# This will run every time we create push a commit to `main`.
# Reference: https://docs.github.com/en/actions/learn-github-actions/events-that-trigger-workflows
on:
  workflow_dispatch:
  push:
    branches:
      - main

# Reference: https://docs.github.com/en/actions/security-guides/automatic-token-authentication
permissions:
  # Need `contents: read` to checkout the repository.
  # Need `contents: write` to update the step metadata.
  contents: write

jobs:
  # Get the current step to only run the main job when the learner is on the same step.
  get_current_step:
    name: Check current step number
    runs-on: ubuntu-latest
    steps:
      - name: Checkout
        uses: actions/checkout@v3
      - id: get_step
        run: |
          echo "current_step=$(cat ./.github/steps/-step.txt)" >> $GITHUB_OUTPUT
    outputs:
      current_step: ${{ steps.get_step.outputs.current_step }}

  on_merge_your_pull_request:
    name: On merge your pull request
    needs: get_current_step

    # We will only run this action when:
    # 1. This repository isn't the template repository.
    # 2. The step is currently 4.
    # Reference: https://docs.github.com/en/actions/learn-github-actions/contexts
    # Reference: https://docs.github.com/en/actions/learn-github-actions/expressions
    if: >-
      ${{ !github.event.repository.is_template
          && needs.get_current_step.outputs.current_step == 4 }}

    # We'll run Ubuntu for performance instead of Mac or Windows.
    runs-on: ubuntu-latest

    steps:
      # We'll need to check out the repository so that we can edit the README.
      - name: Checkout
        uses: actions/checkout@v3
        with:
          fetch-depth: 0 # Let's get all the branches.

      # In README.md, switch step 4 for step X.
      - name: Update to step X
        uses: skills/action-update-step@v2
        with:
          token: ${{ secrets.GITHUB_TOKEN }}
          from_step: 4
          to_step: X
          branch_name: my-first-branch
