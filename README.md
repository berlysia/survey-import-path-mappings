# sandbox-path-alias

## requirements

- Node v20

## how to run

```
npm ci
npm run start # samples it works
npm run broken # samples it does not work
```

# what is this?

There are several ways to alias import paths. The purpose of this repository is to see how to express them, their expressiveness, and their interoperability.

## Expressiveness

| Tool and conditions                                                                                                                                                                         | Prefix replacement `"@/"` -> `"./src/"` | Exact replacement `"libfoo"` -> `"libbar"` | Capture(single⚠️、multiple✅) | RegExp | note                                                                                                                                                                                                                                                                                                                                                                |
| ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | --------------------------------------- | ------------------------------------------ | ----------------------------- | ------ | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Node.js v21.6.0 ESM                                                                                                                                                                         | ✅[^Node]                               | ✅                                         | ⚠️                            | ❌     | -                                                                                                                                                                                                                                                                                                                                                                   |
| Node.js v21.6.0 CJS                                                                                                                                                                         | ✅[^Node]                               | ✅                                         | ⚠️                            | ❌     | Extension guessing does not work                                                                                                                                                                                                                                                                                                                                    |
| Bun v1.0.23, CJS/ESM                                                                                                                                                                        | ✅                                      | ✅                                         | ⚠️                            | ❌     | Node.js (imports field)                                                                                                                                                                                                                                                                                                                                             |
| Bun v1.0.23, TypeScript                                                                                                                                                                     | ✅                                      | ✅                                         | ⚠️                            | ❌     | parse jsconfig, tsconfig [:link:](https://github.com/oven-sh/bun/blob/ff965af0675561c94a6add7cf8227d9a532f49b6/src/resolver/tsconfig_json.zig#L225)                                                                                                                                                                                                                 |
| Deno (application), [Import Maps](https://docs.deno.com/runtime/manual/basics/import_maps)                                                                                                  | ✅                                      | ✅                                         | ❌                            | ❌     | -                                                                                                                                                                                                                                                                                                                                                                   |
| Deno (library), [:link:1](https://docs.deno.com/runtime/manual/basics/import_maps#import-maps-are-for-applications), [:link:2](https://docs.deno.com/runtime/tutorials/manage_dependencies) | ❌                                      | ❌                                         | ❌                            | ❌     | -                                                                                                                                                                                                                                                                                                                                                                   |
| TypeScript v5.3.3, [`paths`](https://www.typescriptlang.org/tsconfig#paths)                                                                                                                 | ✅                                      | ✅                                         | ⚠️                            | ❌     | -                                                                                                                                                                                                                                                                                                                                                                   |
| TypeScript v5.3.3, [`resolvePackageJsonImports`](https://www.typescriptlang.org/tsconfig#resolvePackageJsonImports)                                                                         | ✅[^Node]                               | ✅                                         | ⚠️                            | ❌     | Node.js (imports field), auto-import is [coming soon](https://github.com/microsoft/TypeScript/pull/55015)                                                                                                                                                                                                                                                           |
| Flow                                                                                                                                                                                        | ✅                                      | ✅                                         | ✅                            | ✅     | -                                                                                                                                                                                                                                                                                                                                                                   |
| Webpack v5.89.0 + resolve.alias                                                                                                                                                             | ✅                                      | ✅                                         | ❌                            | ❌     | -                                                                                                                                                                                                                                                                                                                                                                   |
| Webpack v5.89.0 + NormalModuleReplacementPlugin                                                                                                                                             | ✅                                      | ✅                                         | ✅                            | ✅     | -                                                                                                                                                                                                                                                                                                                                                                   |
| Webpack v5.89.0 + [tsconfig-paths-webpack-plugin](https://www.npmjs.com/package/tsconfig-paths-webpack-plugin) v4.1.0                                                                       | ✅                                      | ✅                                         | ⚠️                            | ❌     | parse tsconfig via [tsconfig-paths](https://www.npmjs.com/package/tsconfig-paths)                                                                                                                                                                                                                                                                                   |
| Rollup + [@rollup/plugin-alias](https://www.npmjs.com/package/@rollup/plugin-alias) v5.1.0                                                                                                  | ✅                                      | ✅                                         | ✅                            | ✅     | -                                                                                                                                                                                                                                                                                                                                                                   |
| Rollup + [@rollup/plugin-typescript](https://www.npmjs.com/package/@rollup/plugin-typescript) v11.1.6                                                                                       | ✅                                      | ✅                                         | ⚠️                            | ❌     | tsconfig via typescript                                                                                                                                                                                                                                                                                                                                             |
| esbuild v0.19.11                                                                                                                                                                            | ✅                                      | ✅                                         | ⚠️                            | ❌     | parse jsconfig, tsconfig [:link:](https://github.com/evanw/esbuild/blob/a652e730ff07b9081470ef6965f3d54daa7b2aab/internal/resolver/resolver.go#L2018)                                                                                                                                                                                                               |
| esbuild + --alias                                                                                                                                                                           | ✅                                      | ❌                                         | ❌                            | ❌     | -                                                                                                                                                                                                                                                                                                                                                                   |
| SWC v1.3.102 for JavaScript, [jsc.paths](https://swc.rs/docs/configuration/compilation#jscpaths)                                                                                            | ✅                                      | ✅                                         | ⚠️                            | ❌     | -                                                                                                                                                                                                                                                                                                                                                                   |
| SWC v1.3.102 for TypeScript                                                                                                                                                                 | ✅                                      | ✅                                         | ⚠️                            | ❌     | parse tsconfig [:link:](https://github.com/swc-project/swc/blob/c7236cf07737655c54559cc7889a26018eb1c02a/crates/swc_ecma_loader/src/resolvers/tsc.rs#L32)                                                                                                                                                                                                           |
| Babel v7.23.7 + [babel-plugin-module-resolver](https://www.npmjs.com/package/babel-plugin-module-resolver)                                                                                  | ✅                                      | ✅                                         | ✅                            | ✅     | -                                                                                                                                                                                                                                                                                                                                                                   |
| Next.js v14                                                                                                                                                                                 | ✅                                      | ✅                                         | ⚠️                            | ❌     | parse tsconfig,jsconfig [:link:webpack](https://github.com/vercel/next.js/blob/2096dfa687be61953fe7ccc6b54deea313a517d9/packages/next/src/build/webpack/plugins/jsconfig-paths-plugin.ts#L165-L170), or [:link:turbopack](https://github.com/vercel/turbo/blob/734c764c61da9d6493f0d6616d10ec58702eafd1/crates/turbopack-ecmascript/src/typescript/resolve.rs#L260) |
| Vite + [resolve.alias](https://vitejs.dev/config/shared-options#resolve-alias)                                                                                                              | ✅                                      | ✅                                         | ❌                            | ❌     | -                                                                                                                                                                                                                                                                                                                                                                   |
| Vite + [vite-tsconfig-paths](https://www.npmjs.com/package/vite-tsconfig-paths)                                                                                                             | ✅                                      | ✅                                         | ⚠️                            | ❌     | parse jsconfig, tsconfig via [tsconfck](https://www.npmjs.com/package/tsconfck) and resolve itself [:link:](https://github.com/aleclarson/vite-tsconfig-paths/blob/ca4e2aa7f3be36facbce0305cc145f9d5f9693d1/src/mappings.ts#L8)                                                                                                                                     |
| Remix, [remix-run/remix#2412](https://github.com/remix-run/remix/pull/2412)                                                                                                                 | ✅                                      | ✅                                         | ⚠️                            | ❌     | parse tsconfig via esbuild? [:link:](https://remix.run/docs/en/main/discussion/introduction#compiler)                                                                                                                                                                                                                                                               |
| Nuxt, [original vite plugin](https://github.com/nuxt/nuxt/blob/28b58c9145796b93de41455a57d4af75cbffaabe/packages/nuxt/src/core/plugins/layer-aliasing.ts#L19)                               | ✅                                      | ✅                                         | ❌                            | ❌     |                                                                                                                                                                                                                                                                                                                                                                     |
| Jest, [moduleNameMapper](https://jestjs.io/docs/configuration#modulenamemapper-objectstring-string--arraystring)                                                                            | ✅                                      | ✅                                         | ✅                            | ✅     | -                                                                                                                                                                                                                                                                                                                                                                   |
| Vitest                                                                                                                                                                                      | ✅                                      | ✅                                         | ❌                            | ❌     | see Vite                                                                                                                                                                                                                                                                                                                                                            |
| ESLint + [eslint-import-resolver-alias](https://www.npmjs.com/packages/eslint-import-resolver-alias)                                                                                        | ✅                                      | ✅                                         | ❌                            | ❌     | -                                                                                                                                                                                                                                                                                                                                                                   |
| ESLint + [eslint-import-resolver-typescript](https://www.npmjs.com/package/eslint-import-resolver-typescript)                                                                               | ✅                                      | ✅                                         | ⚠️                            | ❌     | parse jsconfig, tsconfig and resolve via [get-tsconfig](https://www.npmjs.com/package/get-tsconfig)                                                                                                                                                                                                                                                                 |
| ESLint + [eslint-plugin-resolver-webpack](https://www.npmjs.com/package/eslint-plugin-resolver-webpack)                                                                                     | ✅                                      | ✅                                         | ⚠️                            | ❌     | webpack + resolve.alias                                                                                                                                                                                                                                                                                                                                             |
| ESLint + [eslint-import-resolver-babel-module](https://www.npmjs.com/package/eslint-import-resolver-babel-module)                                                                           | ✅                                      | ✅                                         | ⚠️                            | ❌     | babel-plugin-module-resolver                                                                                                                                                                                                                                                                                                                                        |
| Biome Linter                                                                                                                                                                                | ❌                                      | ❌                                         | ❌                            | ❌     | -                                                                                                                                                                                                                                                                                                                                                                   |
| Oxlint                                                                                                                                                                                      | ❌                                      | ❌                                         | ❌                            | ❌     | -                                                                                                                                                                                                                                                                                                                                                                   |

[^Node]: Must be a path that starts with `#` and does not start with `#/`
