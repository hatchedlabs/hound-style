# JavaScript

Generic ES6 style rules for JavaScript programming. Specific plugins (for example flow, jest, react, etc.) can and should be added to each project that requires them.

Requires EsLint which is installed and used by Hound. Locally, installing via **npm** should work:

    npm install -g eslint

## Server side JavaScript

Some settings should probably be different for applications or scripts that are not built to run in the browser:

    "env": {
        "browser": false
    },
    "rules": {
        "no-console": 0
    }

