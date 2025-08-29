# testing

do not write new test files unless asked. do not write tests if there is not already a test or describe block for that function or module.

tests should validate complex and non obvious logic, if a test looks like a placeholder, do not add it.

Use vitest to run tests. Tests should be run from the current package directory and not root, try using the test script instead of vitest directly. Additional vitest flags can be added at the end, like --run to disable watch mode or -u to update snapshots.

To understand how the code you are writing works you should add inline snapshots in the test files with expect().toMatchInlineSnapshot(), then run the test with `pnpm test -u --run` or `pnpm vitest -u --run` to update the snapshot in the file, then read the file again to inspect the result. If the result is not expected, update the code and repeat until the snapshot matches your expectations. Never write the inline snapshots in test files yourself. Just leave them empty and run `pnpm test -u --run` to update them.

> Always call `pnpm vitest` or `pnpm test` with `--run` or they will hang forever waiting for changes!
> ALWAYS read back the test if you use the `-u` option, to make sure the inline snapshot are as you expect.

- for very long snapshots you should use `toMatchFileSnapshot(filename)` instead of `toMatchInlineSnapshot()`. Put the snapshots files in a snapshots/ directory and use the appropriate extension for the file based on the content

Never test client React components. Only server code that runs on the server.

Most tests should be simple calls to functions with some expect calls, no mocks. Test files should be called same as the file where the tested function is being exported from.

NEVER use mocks. the database does not need to be mocked, just use it. simply do not test functions that mutate the database if not asked.

Tests should strive to be as simple as possible, the best test is a simple `.toMatchInlineSnapshot()` call. These can be easily evaluated reading the test file after the run passing the -u option. You can clearly see from the inline snapshot if the function behaves as expected or not.

Try to use only describe and test in your tests. Do not use beforeAll, before, etc if not strictly required.

NEVER write tests for React components or React hooks. NEVER write tests for React components. You will be fired if you do.

Sometimes tests work directly on database data, using prisma. To run these tests you have to use the package.json script, which will call `doppler run -- vitest` or similar. Never run doppler cli yourself as you could delete or update production data. Tests generally use a staging database instead.

Never write tests yourself that call prisma or interact with database or emails. For these asks the user to write them for you.