# README

You have stumbled upon the dialup.world blog. It's not so much a blog as it is just a place to house more long-term projects and information that are not _just_ the curretly-running dial-up ISP.

## Build Notes

Notes for future me. Hopefully this saves some time in the future.

This ia a Jekyll site. The theme it uses is [no-style-please](https://github.com/riggraz/no-style-please), and while this theme is beautiful it is not without its faults. This theme needs an older version of Jekyll, so we have it locked down to version `3.9`. Now, it seems we need this version because the theme uses `jektex` which doesn not play nicely with newer versions. Because we need this version of Jekyll, we also lock our Ruby version at `3.1`. Now, because Jekyll is lower than some `4.x` version that would be nice to have, it doesn't like loading theme assets when the site is built for something other than the `baseURL`. So because we want this to be at `/blog` instead of `/` the theme completely breaks. Hency the `_layouts` (I think), `_includes`, and `_sass` directories. I think I ripped most of the other stuff out so it will work properly.

Also note that again, since we want this to be at `blog` we need to use a custom GHA to build/deploy the site so it is of utmost importance to not use the special, blessed `github-pages` gem because that just messes everything up.
