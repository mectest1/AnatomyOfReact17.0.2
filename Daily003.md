* Daily003: On React Fiber

1, Article by Andew Clarke: React Fiber Architecture
   ref: <https://github.com/acdlite/react-fiber-architecture>

   1,

   2, 


2, Article by Max Koretskyi: Insider Fiber: an in-depth overview of the new reconciliation algorithm in React
   ref: <https://blog.ag-grid.com/inside-fiber-an-in-depth-overview-of-the-new-reconciliation-algorithm-in-react/>


   1,

   2,

3, On re-organizing the React Code:
   1, Only function internals could be re-written at this moment;
   2, Eslint: Add directory public/js to the .eslintignore file
   3,


4, More references:
   1, React Blog: ref: <https://legacy.reactjs.org/blog/>
   2, React All Blog Posts: ref: <https://legacy.reactjs.org/blog/all.html/>
   3, Article by Dan Abramov: React Components, Elements, and Instances
      ref: <https://legacy.reactjs.org/blog/2015/12/18/react-components-elements-and-instances.html#elements-describe-the-tree>
   4, New React blogs: ref: <https://react.dev/blog>
   5, Article: Deep Dev React Fiber
      ref: <https://blog.logrocket.com/deep-dive-react-fiber/>

      //
      Takeaways:
      ...This means that when you call ReactDOm.render() or setState(), React performs reconciliation.
      Terms: Stack Reconciler vs. Fiber Reconciler.

      //
      beginWork()
      performUnitOfWork()
      completeUnitOfWork()
      completeWork()
   6, 

5, Q: How to run and debug jest test cases in local env?
   A: Try to run jest test case in VS Code directory, got the following error from scrips/jest/dont-run-jest-directly.js:
   
          Don't run `jest` directly. Run `yarn test` instead.

      1 | 'use strict';
      2 | 
    > 3 | throw new Error("Don't run `jest` directly. Run `yarn test` instead.");
        |       ^
      4 | 

      at Object.<anonymous> (scripts/jest/dont-run-jest-directly.js:3:7)

   //
   Q: How to test single file with "yarn test"?
   A: Try to run `yarn test test-file`
   ref: <https://stackoverflow.com/questions/28725955/how-do-i-test-a-single-file-using-jest>
   
   e.g.
   ```
   yarn test packages/react/src/__tests__/forwardRef-test.internal.js
   ```

   Test run result:
   ```
   $ node ./scripts/jest/jest-cli.js packages/react/src/__tests__/forwardRef-test.internal.js
   $ NODE_ENV=development RELEASE_CHANNEL=experimental node ./scripts/jest/jest.js --config ./scripts/jest/config.source.js packages/react/src/__tests__/forwardRef-test.internal.js

   Running tests for default (experimental)...
    PASS  packages/react/src/__tests__/forwardRef-test.internal.js
     forwardRef
      √ should work without a ref to be forwarded (612ms)
      √ should forward a ref for a single child (58ms)
      √ should forward a ref for multiple children (58ms)
      √ should maintain child instance and ref through updates (63ms)
      √ should not break lifecycle error handling (82ms)
      √ should not re-run the render callback on a deep setState (69ms)

   Test Suites: 1 passed, 1 total
   Tests:       6 passed, 6 total
   Snapshots:   0 total
   Time:        6.032s
   Ran all test suites matching /packages\\react\\src\\__tests__\\forwardRef-test.internal.js/i.
   Done in 11.74s.
   ```


   //
   Note that the first run might take some extra time. Following test runs might be quicker.

   Related components:
   1, scripts/jest/jest-cli.js
   2, package.json::scripts: debug-test
   3, ref: <https://classic.yarnpkg.com/lang/en/docs/cli/test/>

   //
   In order to debug a specifc file, run `yarn test --debug test-file`, before following instructions on CLI window to proceed, i.e.
   1, Open chrome://inspect and press "inspect"
   2, Click "Add folder to workspace" to add source directory into the debug window;
   3, Add breakpoint and press F8 to continue execution.
   

6, Q: How to jest test a single test case in a specific test file?
   A: Follow the Jest CLI options `-t`.

   For example, to run test case "forwardRef" in forwardRef-test.internal.js, try the following command:
   ```
   yarn test -t "forwardRef" packages/react/src/__tests__/forwardRef-test.internal.js
   yarn run v1.22.19
   $ node ./scripts/jest/jest-cli.js -t forwardRef packages/react/src/__tests__/forwardRef-test.internal.js
   $ NODE_ENV=development RELEASE_CHANNEL=experimental node ./scripts/jest/jest.js --config ./scripts/jest/config.source.js -t forwardRef packages/react/src/__tests__/forwardRef-test.internal.js

   Running tests for default (experimental)...
    PASS  packages/react/src/__tests__/forwardRef-test.internal.js
    forwardRef
    √ should work without a ref to be forwarded (651ms)
    √ should forward a ref for a single child (61ms)
    √ should forward a ref for multiple children (60ms)
    √ should maintain child instance and ref through updates (63ms)
    √ should not break lifecycle error handling (85ms)
    √ should not re-run the render callback on a deep setState (68ms)

   Test Suites: 1 passed, 1 total
   Tests:       6 passed, 6 total
   Snapshots:   0 total
   Time:        6.353s
   Ran all test suites matching /packages\\react\\src\\__tests__\\forwardRef-test.internal.js/i with tests matching "forwardRef".
   Done in 12.09s.
   ```

   //
   Similar invalid matching pattern result could seen as below:
   ```
   yarn test -t "derp" packages/react/src/__tests__/forwardRef-test.internal.js
   yarn run v1.22.19
   $ node ./scripts/jest/jest-cli.js -t derp packages/react/src/__tests__/forwardRef-test.internal.js
   $ NODE_ENV=development RELEASE_CHANNEL=experimental node ./scripts/jest/jest.js --config ./scripts/jest/config.source.js -t derp packages/react/src/__tests__/forwardRef-test.internal.js

   Running tests for default (experimental)...
   Test Suites: 1 skipped, 0 of 1 total
   Tests:       6 skipped, 6 total
   Snapshots:   0 total
   Time:        5.568s
   Ran all test suites matching /packages\\react\\src\\__tests__\\forwardRef-test.internal.js/i with tests matching "derp".
   Done in 9.58s.
   ```


   ref: <https://jestjs.io/docs/cli>
   ref2: <https://stackoverflow.com/questions/28725955/how-do-i-test-a-single-file-using-jest>


7, 
