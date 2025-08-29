# package manager: pnpm with workspace

This project uses pnpm workspaces to manage dependencies. Important scripts are in the root package.json or various packages package.json

try to run commands inside the package folder that you are working on. for example you should never run `pnpm test` from the root

if you need to install packages always use pnpm

instead of adding packages directly in package.json use `pnpm install package` inside the right workspace folder. NEVER manually add a package by updating package.json

## fixing duplicate pnpm dependencies

sometimes typescript will fail if there are 2 duplicate packages in the workspace node_modules. this can happen in pnpm if a package is usedin 2 different places (even if inside a node_module package, transitive dependency) with a different set of versions for a peer dependency

for example if better-auth depends on zod peer dep and zod is in different versions in 2 dependency subtrees

to identify if a pnpm package is duplicated search for the string " packagename@" inside `pnpm-lock.yaml`, notice the space in the search string. Then if the result returns multiple instances with a different set of peer deps inside the round brackets it means that this package is being duplicated. Here is an example of a package getting duplicated:

```

  better-auth@1.3.6(react-dom@19.1.1(react@19.1.1))(react@19.1.1)(zod@3.25.76):
    dependencies:
      '@better-auth/utils': 0.2.6
      '@better-fetch/fetch': 1.1.18
      '@noble/ciphers': 0.6.0
      '@noble/hashes': 1.8.0
      '@simplewebauthn/browser': 13.1.2
      '@simplewebauthn/server': 13.1.2
      better-call: 1.0.13
      defu: 6.1.4
      jose: 5.10.0
      kysely: 0.28.5
      nanostores: 0.11.4
      zod: 3.25.76
    optionalDependencies:
      react: 19.1.1
      react-dom: 19.1.1(react@19.1.1)

  better-auth@1.3.6(react-dom@19.1.1(react@19.1.1))(react@19.1.1)(zod@4.0.17):
    dependencies:
      '@better-auth/utils': 0.2.6
      '@better-fetch/fetch': 1.1.18
      '@noble/ciphers': 0.6.0
      '@noble/hashes': 1.8.0
      '@simplewebauthn/browser': 13.1.2
      '@simplewebauthn/server': 13.1.2
      better-call: 1.0.13
      defu: 6.1.4
      jose: 5.10.0
      kysely: 0.28.5
      nanostores: 0.11.4
      zod: 4.0.17
    optionalDependencies:
      react: 19.1.1
      react-dom: 19.1.1(react@19.1.1)

```

As you can see better-auth is listed twice with different set of peer deps. In this case it's because of zod being in version 3 and 4 in two subtrees of our workspace dependencies.

As a first step try running `pnpm dedupe better-auth` with your package name and see if there is still the problem.

below i will describe how to generally deduplicate a package, I will use zod as an example. It works with any dependency found in the previous step.

To deduplicate the package we have to make sure we only have 1 version of zod installed in your workspace. DO NOT use overrides for this. Instead fix the problem by manually updating the dependencies that are forcing the older version of zod in the dependency tree.

to do so we have first to run the command `pnpm -r why zod@3.25.76` to see the reason the older zod version is installed. In this case the result is something like this:

```

website /Users/morse/Documents/GitHub/holocron/website (PRIVATE)

dependencies:
@better-auth/stripe 1.2.10
├─┬ better-auth 1.3.6
│ └── zod 3.25.76 peer
└── zod 3.25.76
db link:../db
└─┬ docs-website link:../docs-website
  ├─┬ fumadocs-docgen 2.0.1
  │ └── zod 3.25.76
  ├─┬ fumadocs-openapi link:../fumadocs/packages/openapi
  │ └─┬ @modelcontextprotocol/sdk 1.17.3
  │   ├── zod 3.25.76
  │   └─┬ zod-to-json-schema 3.24.6
  │     └── zod 3.25.76 peer
  └─┬ searchapi link:../searchapi
    └─┬ agents 0.0.109
      ├─┬ @modelcontextprotocol/sdk 1.17.3
      │ ├── zod 3.25.76
      │ └─┬ zod-to-json-schema 3.24.6
      │   └── zod 3.25.76 peer
      └─┬ ai 4.3.19
        ├─┬ @ai-sdk/provider-utils 2.2.8
        │ └── zod 3.25.76 peer
        └─┬ @ai-sdk/react 1.2.12
          ├─┬ @ai-sdk/provider-utils 2.2.8
          │ └── zod 3.25.76 peer
          └─┬ @ai-sdk/ui-utils 1.2.11
            └─┬ @ai-sdk/provider-utils 2.2.8
              └── zod 3.25.76 peer
```

Here we can see zod 3 is installed because of @modelcontextprotocol/sdk, @better-auth/stripe and agents packages. To fix the problem we can run

```
pnpm update -r --latest  @modelcontextprotocol/sdk @better-auth/stripe agents
```

This way if these packages include the newer version of the dependency zod will be deduplicated automatically.

In this case we could have only updated only @better-auth/stripe to fix the issue too, that's becaues @better-auth/stripe is the one that has better-auth as a peer dep. But finding what is the exact problematic package is difficult so it is easier to just update all packages you notice that we depend on directly in our workspace package.json files.

IF after doing this we still have duplicate packages you will have to ask help to the user. You can try deleting the node_modules and restart the approach but it rarely helps.