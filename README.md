# WordPress.org Plugin Deploy

This Action commits the contents of your Git tag to the WordPress.org plugin repository using the same tag name. It can exclude files as defined in `.distignore`, and moves anything from a `.wordpress-org` subdirectory to the top-level `assets` directory in Subversion (plugin banners, icons, and screenshots).

The code forked from <https://github.com/10up/action-wordpress-plugin-deploy/> and slightly simplified for our own needs. Also added `SOURCE_DIR` variable support.

## Configuration

### Required secrets

* `SVN_USERNAME`
* `SVN_PASSWORD`

[Secrets are set in your repository settings](https://help.github.com/en/actions/automating-your-workflow-with-github-actions/creating-and-using-encrypted-secrets). They cannot be viewed once stored.

### Optional environment variables

* `SLUG` - defaults to the repository name, customizable in case your WordPress repository has a different slug or is capitalized differently.
* `VERSION` - defaults to the tag name; do not recommend setting this except for testing purposes.
* `SOURCE_DIR` - defaults to `/` root directory of your repository. If you use custom build directory, it will be useful to customize to something like this `build/`.
* `ASSETS_DIR` - defaults to `.wordpress-org`, customizable for other locations of WordPress.org plugin repository-specific assets that belong in the top-level `assets` directory (the one on the same level as `trunk`).

## Excluding files from deployment

If there are files or directories to be excluded from deployment, such as tests or editor config files, they can be specified in either a `.distignore` file using the `export-ignore` directive. If a `.distignore` file is present, it will be used.

`.distignore` is useful particularly when there are built files that are in `.gitignore`, and is a file that is used in [WP-CLI](https://wp-cli.org/). For modern plugin setups with a build step and no built files committed to the repository, this is the way forward.

### Sample baseline files

#### `.distignore`

**Notes:** `.distignore` is for files to be ignored **only**. This comes from its current expected syntax in WP-CLI's [`wp dist-archive` command](https://github.com/wp-cli/dist-archive-command/).

```
/.wordpress-org
/.git
/.github
/node_modules
.distignore
.gitignore
```

## Example Workflow File

```yml
name: Deploy to WordPress.org
on:
  push:
    tags:
    - "v*"
  pull_request:
    tags:
    - "v*"

jobs:
  deploy:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@master

      - name: Build
        run: |
          npm install
          npm run build

      - name: WordPress Plugin Deploy
        uses: nk-o/action-wordpress-plugin-deploy@master
        env:
          SVN_PASSWORD: ${{ secrets.SVN_PASSWORD }}
          SVN_USERNAME: ${{ secrets.SVN_USERNAME }}
          SOURCE_DIR: dist/
          SLUG: my-plugin-name
```

## Thanks to

This action is based on 10up action, so we need to thank [for code](https://github.com/10up/action-wordpress-plugin-deploy/) to these guys <http://10up.com/>
