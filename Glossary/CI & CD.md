---
aliases:
    - CI/CD
    - Continuous Integration & Continuous Delivery
---

- CI/CD stands for Continuous Integration and Continuous Delivery/Deployment.
- It is a software development practice that aims to streamline and accelerate the [[SDLC|Software Development Lifecycle]].
- It automates the entire software development process from code commit all the way thru deployment.

> [!cite] Source: ByteByteGo
> ![CI/CD](assets/images/ci-cd.png)

- Each commit triggers an automated workflow on a CI server.
- Continuous Integration (CI) involves automatically integrating code changes into a shared repo frequently, triggering automated testing to ensure the reliability of merged code changes.
- Continuous Delivery (CD) involves automating the release of validated code to a repo following automated builds and unit testing.
    - Continuous Deployment is an extension of Continuous Delivery where changes are automatically released to production.
- The CI/CD workflow focuses on automation, tight version control, integrated feedback loops, security practices, and rapid iterative cycles to enhance software quality and speed up development processes.

> [!cite] Source: ByteByteGo
> ![CI/CD Pipelines](assets/images/ci-cd.pipeline.jpg)

- A typical CI/CD workflow:
    - The developer commits code changes to source control
    - The CI server detects changes and triggers the build
    - After code is compiled, different tests are run, and results are reported to the developer.
    - If tests are successful, artifacts are deployed to a staging environment where further testing may be done before release.
    - The CD system deploys approved changes to production

## Example

```yml
name: GitHub Actions CI/CD Demo

on:
    push:
        branches:
            - main

jobs:
    build:
        runs-on: ubuntu-latest
        steps:
            - name: Get Code
              uses: actions/checkout@v2
            - name: Setup Node.js
              uses: actions/setup-node@v1
              with:
                  node-version: 16

            - name: Install Dependencies
              run: npm ci

            - name: Run Test Script
              run: npm test

            - name: Run Build Script
              run: npm run build

            - name: Install Dependencies
              run: npm run deploy
```

## GitHub Actions

![[GitHub Actions]]
