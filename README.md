# GitHub Actions Manual Approval

This repo has an example Action that requires manual approval before running.

Background information:

- https://docs.github.com/en/actions/deployment/about-deployments/deploying-with-github-actions
- https://docs.github.com/en/actions/deployment/targeting-different-environments/using-environments-for-deployment

## Setup

1. in the GitHub repo settings, go to Environment and add a new environment. in
   this example, it's named `test`, which is used in the workflow job to require
   approval before running
2. create a simple github actions, in this case the full example is in
   [`.github/workflows/build_and_release.yml`](.github/workflows/build_and_release.yml):

   ```yml
   name: test-actions-approval

   on:
     push:
       branches:
       - main
     create:
       tags:
     pull_request:
       branches:
       - main

   jobs:
     build:
       runs-on: ubuntu-latest
       steps:
       - name: "building ref: ${{ github.ref }}"
           run: |
           echo yolo
   ```

3. add a second job that references the environment, so it requires manual
   approval:

   ```yml
     deploy:
       if: (github.event_name == 'create') && (startsWith(github.ref, 'refs/tags/'))
       runs-on: "ubuntu-latest"
       # setting the environment here requires approval before the job will run
       environment: "test"
       needs: build
       steps:
         - name: approval complete ➡️ 🚢
           run: |
             echo 'shipping!'
   ```
