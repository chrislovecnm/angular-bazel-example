[![CircleCI](https://circleci.com/gh/alexeagle/angular-bazel-example.svg?style=svg)](https://circleci.com/gh/alexeagle/angular-bazel-example)

# Example of building an Angular app with Bazel

**This is experimental! There may be breaking changes.**

This is part of the ABC project. The overall goal is to make it possible to
develop Angular applications the same way we do at Google.
See http://g.co/ng/abc for an overview.

You can read the documentation in the wiki of this repository to understand how
this works.

Follow https://github.com/angular/angular/issues/19058 for updates.

## Installation

Add `ibazel` to your `$PATH`:

```
yarn global add @bazel/ibazel
```

or

```
npm install -g @bazel/ibazel
```

## Development

Next we'll run the development server:

```bash
$ yarn serve
```

> This script runs `ibazel`, which is a "watch mode"
> for Bazel. That means it will watch any files that are inputs to the devserver,
> and when they change it will ask Bazel to re-build them. The server stays
> running, and when the re-build is finished, it will trigger the LiveReload in
> the browser.

This command prints a URL on the terminal. Open that page to see the demo app
running. Now you can edit one of the source files (`src/lib/file.ts` is an easy
one to understand and see the effect). As soon as you save a change, the app
should refresh in the browser with the new content. Our intent is that this time
is less than two seconds, even for a large application.

Control-C twice to kill the devserver and also stop `ibazel`.

## Testing

We can also run all the unit tests:

```bash
$ yarn test
```

Or run the end-to-end tests:

```bash
$ yarn e2e
```

In this example, there is a unit test for the `hello-world` component which uses
the `ts_web_test_suite` rule. There are also protractor e2e tests for both the
`prodserver` and `devserver` which use the `protractor_web_test_suite` rule.

Note that Bazel will only re-run the tests whose inputs changed since the last run.

## Production

We can run the application in production mode, where the code has been bundled
and optimized. This can be slower than the development mode, because any change
requires re-optimizing the app. This example uses Rollup and Uglify, but other
bundlers can be integrated with Bazel.

```bash
$ yarn serve-prod
```

### Code splitting

The production bundle is code split and the `/` and `/todos` routes
are lazy loaded. Code splitting is handled by the rollup_bundle rule
which now supports the new code splitting feature in rollup.

Note: code splitting is _not_ supported in development mode yet so the
`//src:devserver` target does not serve a code split bundle. For this
reason, development and production use different main entry points
(`main.dev.ts` and `main.ts`) and different root modules
(`app.module.dev.ts` and `app.module.ts`). The difference in
the entry points and modules is how routes are loaded, with production
lazy loading routes and development using a custom `NgModuleFactoryLoader`
loader to disable lazy loading. `enableProdMode()` is
also called in the production entry point.

## Npm dependencies

Having a local `node_modules` folder setup by `yarn` or `npm` is not
necessary when building this example with Bazel. This example makes use
of Bazel managed npm dependencies (https://github.com/bazelbuild/rules_nodejs#using-bazel-managed-dependencies)
which means Bazel will setup the npm dependencies in your `package.json` for you
outside of your local workspace for use in the build.

However, you may still want to run `yarn` or `npm` to manually
setup a local `node_modules` folder for editor and tooling support.
