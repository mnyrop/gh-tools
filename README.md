# gh-tools :octocat:

## getting started

1. generate a personal access token (if working with private repos)
2. copy `.env-template` to `.env` with `cp env-template .env`
3. add personal access token and repo of interest (e.g., `mnyrop/gh-tools`) to `.env` (*this file is .gitignored*)
4. run `bundle install` to install dependencies
5. run rake tasks. to see options, run `bundle exec rake --tasks`
