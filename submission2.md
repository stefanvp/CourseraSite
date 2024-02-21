# What task(s) did you automate?

Building the Jekyll site using newer versions of Ruby and Jekyll. It also automatically deploys the build site.			 					

# Why did you choose to automate the aforementioned task(s)?

Using a check to make sure the build succeeds guarantees a working code and deploying the build site gets rid of time wasted uploading to the host, github in this case. 

# How did you automate the aforementioned task(s)?

The build-task is automated using Ubuntu, Ruby, and Jekyll.
The deploy-task uses the provided actions/deploy-pages@v4 action, by github.

# Upload a copy of the GitHub Actions workflow. 
```yaml
name: Deploy Jekyll site to Pages

on:
  push:
    branches: ["main"]
  workflow_dispatch:

permissions:
  contents: read
  pages: write
  id-token: write

concurrency:
  group: "pages"
  cancel-in-progress: false

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout
        uses: actions/checkout@v4
      - name: Setup Ruby
        uses: ruby/setup-ruby@8575951200e472d5f2d95c625da0c7bec8217c42 # v1.161.0
        with:
          ruby-version: '3.1'
          bundler-cache: true
          cache-version: 0
      - name: Setup Pages
        id: pages
        uses: actions/configure-pages@v4
      - name: Build with Jekyll
        run: bundle exec jekyll build --baseurl "${{ steps.pages.outputs.base_path }}"
        env:
          JEKYLL_ENV: production
      - name: Upload artifact
        uses: actions/upload-pages-artifact@v3

  deploy:
    environment:
      name: github-pages
      url: ${{ steps.deployment.outputs.page_url }}
    runs-on: ubuntu-latest
    needs: build
    steps:
      - name: Deploy to GitHub Pages
        id: deployment
        uses: actions/deploy-pages@v4
```
