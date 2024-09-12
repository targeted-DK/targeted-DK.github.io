source "https://rubygems.org"

# Hello! This is where you manage which Jekyll version is used to run.
# Let GitHub Pages manage the Jekyll version, so we don't need to specify it here.

# This is the default theme for new Jekyll sites. You may change this to anything you like.
gem "minima", "~> 2.5"
gem "webrick"

# Use GitHub Pages, which will manage the correct Jekyll version and other dependencies
gem "github-pages", group: :jekyll_plugins

# If you have any plugins, put them here!
group :jekyll_plugins do
  gem "jekyll-feed", "~> 0.12"
  gem "jekyll-octicons"
end

gem "jekyll-github-metadata"
gem "jekyll-theme-text"


# Windows and JRuby does not include zoneinfo files, so bundle the tzinfo-data gem
# and associated library.
install_if -> { RUBY_PLATFORM =~ %r!mingw|mswin|java! } do
  gem "tzinfo", "~> 1.2"
  gem "tzinfo-data"
end

# Performance-booster for watching directories on Windows
gem "wdm", "~> 0.1.1", install
