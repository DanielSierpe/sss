Vitest caught 2 unhandled errors during the test run.
This might cause false positive tests. Resolve unhandled errors to make sure your tests are not affected.

⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯ Unhandled Rejection ⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯
ReferenceError: Cannot access 'cfg' before initialization
 ❯ Object.<anonymous> src/services/__tests__/AuthService.test.ts:4:70
      3| // Mock de localStorage
      4| const localStorageMock = {
      5|   getItem: vi.fn(),
       |                      ^
      6|   setItem: vi.fn(),
      7|   removeItem: vi.fn(),
 ❯ Object.mockCall node_modules/@vitest/spy/dist/index.js:61:17
 ❯ Object.spy node_modules/tinyspy/dist/index.js:45:80
 ❯ new AuthService src/services/AuthService.ts:16:19
 ❯ src/services/AuthService.ts:251:16
 ❯ processTicksAndRejections node:internal/process/task_queues:105:5
 ❯ VitestExecutor.runModule node_modules/vite-node/dist/client.mjs:399:5
 ❯ VitestExecutor.directRequest node_modules/vite-node/dist/client.mjs:381:5
 ❯ VitestExecutor.cachedRequest node_modules/vite-node/dist/client.mjs:206:14
 ❯ VitestExecutor.dependencyRequest node_modules/vite-node/dist/client.mjs:259:12

This error originated in "src/services/__tests__/AuthService.test.ts" test file. It doesn't mean the error was thrown inside the file itself, but while it was running.
The latest test that might've caused the error is "src/services/__tests__/AuthService.test.ts". It might mean one of the following:
- The error was thrown, while Vitest was running this test.
- If the error occurred after the test had been completed, this was the last documented test before it was thrown.

⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯ Unhandled Rejection ⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯
ReferenceError: Cannot access 'cfg' before initialization
 ❯ Object.<anonymous> src/services/__tests__/AuthService.test.ts:4:70
      3| // Mock de localStorage
      4| const localStorageMock = {
      5|   getItem: vi.fn(),
       |                      ^
      6|   setItem: vi.fn(),
      7|   removeItem: vi.fn(),
 ❯ Object.mockCall node_modules/@vitest/spy/dist/index.js:61:17
 ❯ Object.spy node_modules/tinyspy/dist/index.js:45:80
 ❯ new AuthService src/services/AuthService.ts:17:19
 ❯ src/services/AuthService.ts:251:16
 ❯ processTicksAndRejections node:internal/process/task_queues:105:5
 ❯ VitestExecutor.runModule node_modules/vite-node/dist/client.mjs:399:5
 ❯ VitestExecutor.directRequest node_modules/vite-node/dist/client.mjs:381:5
 ❯ VitestExecutor.cachedRequest node_modules/vite-node/dist/client.mjs:206:14
 ❯ VitestExecutor.dependencyRequest node_modules/vite-node/dist/client.mjs:259:12

This error originated in "src/services/__tests__/AuthService.test.ts" test file. It doesn't mean the error was thrown inside the file itself, but while it was running.
The latest test that might've caused the error is "src/services/__tests__/AuthService.test.ts". It might mean one of the following:
- The error was thrown, while Vitest was running this test.
- If the error occurred after the test had been completed, this was the last documented test before it was thrown.
⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯⎯

 Test Files  15 passed (15)
      Tests  306 passed (306)
     Errors  2 errors
   Start at  15:24:29
   Duration  33.41s (transform 2.21s, setup 7.55s, collect 8.66s, tests 8.91s, environment 52.71s, prepare 5.60s)
