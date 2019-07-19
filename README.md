# hubs-monorepo

Common Hubs and Spoke libraries.

## Contributing

This repository is setup as a monorepo using [lerna](https://lerna.js.org/) and (yarn workspaces)[https://yarnpkg.com/lang/en/docs/workspaces/].

### Setup

If you haven't already, install node, yarn and lerna:

[Install Node](https://nodejs.org/)

Then run:

```
npm install -g yarn
yarn global add lerna
```

### Bootstrapping

The `lerna bootstrap` command is used to install all of the packages dependencies and link dependent packages. You can read more about it [here](https://github.com/lerna/lerna/tree/master/commands/bootstrap#readme).

Before you can build you'll need to bootstrap the project:

```
cd /path/to/hubs-monorepo
lerna bootstrap
```

### Building

To build all packages run the build script in the root directory:

```
cd /path/to/hubs-monorepo
yarn build
```

To build packages on each change run the watch script in the root directory:

```
cd /path/to/hubs-monorepo
yarn watch
```

This build script passes each of the packages to the typescript compiler so that [project references and incremental compiles](https://www.typescriptlang.org/docs/handbook/project-references.html) work correctly. Running `tsc` directly in a package directly may not work correctly. It is advised to always use the build script.

By default the build script builds two targets:
  - cjs - commonjs that uses the commonjs module target in typescript and targets es5 language feature. Available as `main` in the package's package.json
  - esm - esmodules that uses the es2015 module target in typescript and targets es2015 language features. Available as `module` in the package's package.json

In addition typescript definitions and typescript definition mappings are generated for integration with language servers and typescript.

These built files are located in each package's `lib` directory which is included in the published npm package.

### Running the kitchen-sink-example project

The [`kitchen-sink-example`](packages/kitchen-sink-example) package is currently a showcase for all of the modules in this project.

To run it:

```
cd /path/to/hubs-monorepo
yarn kitchen-sink
```

### Linking a package into another project

If you want to use a development build of one of the packages in this repository in another project, use `yarn link`.

For example if you wanted to link [three-particle-emitter](packages/three-particle-emitter) into [hubs](https://github.com/mozilla/hubs):

```
cd /path/to/hubs-monorepo/packages/three-particle-emitter
yarn link

cd /path/to/hubs-monorepo
yarn watch
```

In another terminal session:

```
cd /path/to/hubs
yarn link three-particle-emitter
```

Now three-particle-emitter is linked into hubs and will rebuild whenever it is changed.

### Publishing

TODO

### Creating a new package

Creating a new package in the mono repository is made easier with the `create-package` script.

```
cd /path/to/hubs-monorepo
yarn create-package <template-name> <package-name>
```

Templates are stored in the [templates folder](templates/) and are copied into the packages folder with the correct package name when running the `create-package` command.

Currently there are the following templates:

- `library`
  Creates a typescript library with package name `@mozillareality/{package-name}` and no dependencies.
- `example`
  Creates an example project with three and webpack setup correctly.

#### Project References

If your new package depends on another package in the mono repository you need to specify the [typescript project reference](https://www.typescriptlang.org/docs/handbook/project-references.html) or else that project will not be properly rebuilt on change.

For example see [three-particle-emitter's tsconfig.json file](packages/three-particle-emitter/tsconfig.json):

```
"references": [
  { "path": "../easing-functions" }
]
```

`three-particle-emitter` is dependent on `@mozillareality/easing-functions` located in `packages/easing-functions` so we need to add it as a project reference in `three-particle-emitter`'s `tsconfig.json`.

### Linting / Code Style

This project uses [tslint](https://palantir.github.io/tslint/) and [prettier](https://prettier.io/) for linting and code formatting.

To set `tslint` up in your editor of choice see [this list of tools](https://palantir.github.io/tslint/usage/third-party-tools/).

If you want to run `tslint` directly run:

```
cd /path/to/hubs-monorepo
yarn lint
```