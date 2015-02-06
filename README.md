# symlink bug

This repo demonstrates an apparent error in the way `coffeeify` handles requiring files in directories in `node_module`s.

To see the bug in action:

    $ npm install
    $ node_modules/.bin/browserify -e js-src/foo.js
    # note that this succeeds
    $ node_modules/.bin/browserify -t coffeeify -e coffee-src/foo.coffee
    # note that this fails!

In both the coffee and js versions, there is a symlink called `lib` in the `node_modules` directory of the `-src` directory. The JS version is able to resolve the `require('lib/bar')` correctly, but when using coffeeify this fails.

It appears that coffeeify is doing something strange with the registered require extension that makes it unable to require coffee files in subdirectories of `node_modules`.

Symlinks directly to coffeescript files work correctly, though. As a demonstration of this, see:

    $ node_modules/.bin/browserify -t coffeeify -e coffee-src/foo-require-file.coffee

This doesn't actually seem limited to symlinks. Requiring from a normal directory fails as well:

    $ node_modules/.bin/browserify -t coffeeify -e coffee-src/foo-require-file.coffee

While it's unusual to install a third-party `node_module` that publishes coffeescript source, it's very common to use symlinks to create require namespaces to local files, so I would expect coffeeify to support this pattern.
