---
"@nitorcreations/kysely-data-api": major
---

Support kysely 0.29 and modernize the toolchain.

- Import the migration table constants from the `kysely/migration` subpath,
  which kysely 0.28+ requires now that they are no longer re-exported from the package root.
- Conform the postgres introspector to the kysely 0.29 `DatabaseIntrospector` interface
  (`getTables` returns `TableMetadata[]` with the required `isForeign` field;
  the removed `getMetadata` method is dropped).
- Drop the deprecated `numUpdatedOrDeletedRows` field from driver query results
  in favour of `numAffectedRows`.
- Ship as an ESM-only package targeting Node 24,
  built with TypeScript 6.
