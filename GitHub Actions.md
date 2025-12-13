- GitHub Actions is a platform for automating workflows in the [[SDLC|Software Development Lifecycle]] on GitHub.
- Workflows consist of events, jobs, and steps, and are triggered by events, such as creating a pull request, or pushing to a branch.
    - Events are triggers that kick off a workflow,.
    - Jobs are a set of steps that execute on the same runner.
    - Steps are individual actions.
- GitHub Actions supports multiple operating systems and languages within the same workflow.
- It offers native CI/CD functionality, but can also automate any webhook.
- It integrates seamlessly with GitHub's existing features.
- Benefits of GitHub Actions include:
    - Automating workflows across issues, pull requests, and more.
    - Adding preferred tools and services to a project.
    - Multi-container testing and "matrix builds."
    - Live logs for watching workflows run in real time.

## Example

> [!example] Deploying a Vite App to GitHub Pages

```yml
# .github/workflows/deploy.yml
name: Deploy

on:
    push:
        branches:
            - main

jobs:
    build:
        name: Build
        runs-on: ubuntu-latest

        steps:
            - name: Checkout repo
              uses: actions/checkout@v2

            - name: Setup Node
              uses: actions/setup-node@v1
              with:
                  node-version: 16

            - name: Install dependencies
              run: npm ci

            - name: Build project
              run: npm run build

            - name: Upload production-ready build files
              uses: actions/upload-artifact@v2
              with:
                  name: production-files
                  path: ./dist

    deploy:
        name: Deploy
        needs: build
        runs-on: ubuntu-latest
        if: github.ref == 'refs/heads/main'

        steps:
            - name: Download artifact
              uses: actions/download-artifact@v2
              with:
                  name: production-files
                  path: ./dist

            - name: Deploy to GitHub Pages
              uses: peaceiris/actions-gh-pages@v3
              with:
                  github_token: ${{ secrets.GITHUB_TOKEN }}
                  publish_dir: ./dist
```
