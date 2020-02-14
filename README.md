My blog.
Uses [jekyll](https://jekyllrb.com/).
Hosted on [github pages](https://pages.github.com/).

### Local dev:

1. Install [bundler](https://bundler.io/), [html-tidy](http://www.html-tidy.org/), and [vnu](https://libraries.io/homebrew/vnu).
2. Install dependencies: `bundle`
3. Build and serve the site: `bundle exec rake serve`

The build will fail if html-tidy or vnu errors or warns.

To just run the build without serving: `bundle exec rake build`.

The site is built to the `_site` directory.

### Deploy:

The site is served from the `gh-pages` branch which uses the `_site` dir as a root using the `git worktree` workflow described [here](https://sangsoonam.github.io/2019/02/08/using-git-worktree-to-deploy-github-pages.html).

1. Build the site: `bundle exec rake build`
2. Commit the compiled site:
  * cd `_site`
  * `git add .`
  * `git commit`
  * `git push origin gh-pages`
