My blog.
Uses [jekyll](https://jekyllrb.com/).
Hosted on [github pages](https://pages.github.com/).

### Local dev:

1. Install [bundler](https://bundler.io/).
2. Install dependencies: `bundle`
3. Build and serve the site: `bundle exec rake serve`

The build will fail if [html-tidy](http://www.html-tidy.org/) errors or warns.

To just run the build without serving: `bundle exec rake build`.
