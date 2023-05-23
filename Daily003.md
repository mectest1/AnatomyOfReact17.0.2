# Daily003: On React Fiber

1, Article by Andrew Clarke: React Fiber Architecture

   ref: <https://github.com/acdlite/react-fiber-architecture>

   1, Prerequisites:

      Article from Sebastian Markbage: React - Basic Theoretical Concepts
      ref: <https://github.com/reactjs/react-basic>

      > The core premise for React is that UIs are simply a projection of data into a different form of data.
      
   
   2, React Design Principles
      ref: <https://legacy.reactjs.org/docs/design-principles.html>

      > ...We can prioritize work coming from user interactions (such as an animation caused by a button click) over less important background work (such as rendering new content just loaded from the network) to avoid dropping frames.

      > There is an internal joke in the team that React should have been called "Schedule" because React does not want to be fully "reactive".

      > ...We prefer boring code to clever code. Code is disposable and often changes. So it is important that it doesn't introduce new internal abstractions unless absolutely necessary. Verbose code that is easy to move around, change and remove is preferred to elegant code that is prematurely abstracted and hard to change.
      
   3, Key Concepts:

      - Reconciliation: the algorithm React uses to diff one tree with another to determine which parts need to be changed.
      - Update: a change in the data used to render a React app. Usually the result of `setState`. Eventually results in a re-render.
	  - React is designed so that reconciliation and rendering ar eseparate phases. The reconciler does the work of computing which parts of a tree have chagned; the renderer then uses that information to actually update the rendered app.
	  - Fiber reimplements the reconciler. It is not principlally concerned with rendering.
      - Scheduling: the process of determining when work should be performed
      - Work: any computations that must be performed. Work is usually the result of an update.
      

   4, Purpose of Fiber: enable React to
      - pause work and come back to it later
      - assign priority to different types of work
      - reuse previously completed work
      - abort work if it's no longer needed


   5, A fiber represents a unit of work

   6, requestIdleCallback() and requestAnimationFrame()

   7, Wouldn't it be great if we could cutomize the behavior of the call stack to optimize for rendering UIs? Wouldn't it be great if we could interrupt the call stack at will and manipulate stack frames manually?
   -- That is te purpose of the React Fiber. Fiber is reimplementation of the stack, specialized for React components. You can think of a single fiber as a virtual stack frame.

   8, Structurer of Fiber
      - type and key
      - child and sibling
      - return
      - pendingProps and memoizeprops
      - pendingWorkPriority
      - alternate, flush, and work-in-progress
      - ouptut
      

   9, Remaining questions:
      - how ot scheduler finds the next unit of work to perform
      - how priority is tracked and propagated through the fiber tree
      - how the scheduler knows when to pause and resume work
      - how work is flushed and marked as complete
      - how side-effects (such as lifecycle methods) work
      - what a coroutine is and how it can be used to implement features like context and layout

    


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


7, Daily Dose of Emacs:
   C-x TAB runs command indent-rigidly, which is an interactive native compiled Lips that indents all lines starting in the region.

8, 
