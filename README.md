## Kysely Data Api

This library adds AWS RDS Data Api support for [kysely](https://github.com/koskimas/kysely). It has support for both MySQL and Postgres

### About this fork

`@nitorcreations/kysely-data-api` is a Nitor fork of the upstream [`kysely-data-api`](https://github.com/serverless-stack/kysely-data-api).
It exists because kysely 0.28+ stopped re-exporting the migration table constants
(`DEFAULT_MIGRATION_LOCK_TABLE`, `DEFAULT_MIGRATION_TABLE`) from the package root
and moved them to the `kysely/migration` subpath,
which broke the upstream package against kysely 0.29.
This fork imports those constants from the new location,
conforms the Postgres introspector to the kysely 0.29 `DatabaseIntrospector` interface,
and ships as an ESM-only package targeting Node 24.

### Usage

```typescript
const dataApi = new DataApiDialect({
  mode: "mysql",
  driver: {
    client: new RDSDataService(),
    database: "bench",
    secretArn: "<arn of secret containing credentials",
    resourceArn: "<arn of database>",
  },
});

export const db = new Kysely<Database>({ dialect: dataApi });
```

### Development

Formatting is handled by [oxfmt](https://github.com/oxc-project/oxc) (configured in `.oxfmtrc.json`),
the same formatter Iron Bank uses.
Type checking uses `tsc`.

```bash
pnpm run format        # format with oxfmt
pnpm run format:check  # check formatting without writing
pnpm run typecheck     # tsc --noEmit
```

#### Pre-commit hooks

Install [prek](https://github.com/j178/prek) and activate the Git hooks:

```shell
brew install prek
prek install -f
```

This runs oxfmt and the type-check on every commit (see `.pre-commit-config.yaml`).
To run all hooks manually: `prek run --all-files` (or the short form `prek run -a`).

### Publishing

The package is published to the private Nitor npm registry
(`https://packages.nitor.zone/repository/npm/`, configured via `publishConfig` in `package.json`).

Publishing requires an auth token for that registry in your `~/.npmrc`:

```ini
@nitorcreations:registry=https://packages.nitor.zone/repository/npm/
//packages.nitor.zone/repository/npm/:_authToken=<your-token>
```

To cut a release,
bump the `version` in `package.json` (major/minor/patch as appropriate),
commit it,
then run:

```bash
pnpm publish
```

`publishConfig` routes the publish to the Nitor registry automatically,
and `prepublishOnly` cleans and rebuilds `dist/` first.
The publish requires a clean git tree,
so commit the version bump before running it.
A published version number is immutable,
so bump the version again for any further changes.
