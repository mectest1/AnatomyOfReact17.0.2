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


   1, Q: What does React.createElement() actually returns?
   
      A: Try the following code in browser
      	 `React.createElement.("span", {key: "testSpan"}, "Hello, World")`
	 and here's what we get:

	 ```
	 {
	    "$$typeof": Symbol(react.element)
 	    "type": "span",
	    "key": "testSpan",
	    "ref": null,
	    "props": {
		"children": "Hello, World"
	    },
	    "_owner": null,
	    "_store": {}
	 }
	 ```

      Note that the key props here are:
      	   $typeoff
	   type
	   key
	   props
	   props.children
	   etc.
      

   2, Q: What about calling React.createElement() on custom component?
      A: Try the following code:
      
      ```
      function TestComponent() { return React.createElement('span', {key: 'helloSpan'}, 'Hello, World'); }
      var s = React.createElement(TestComponent, {key: 'testComponent'}, 'Greetings');
      ```

      And the result is:

      ```
      {
	  $$typeof: Symbol(react.element),
	  "type": TestComponent,
	  "key": "testComponent",
	  "ref": null,
	  "props": {
	      "children": "Greetings"
	  },
	  "_owner": null,
	  "_store": {}
      }
      ```
      

   3, Fiber Nodes
   
      During reconciliation data from every Reacrt element returned from the `render` method is merged into the tree of fiber nodes. Every React element has a corresponding fiber node. Unlike React elements, fibers aren't re-created on every rneder. These are mutable structures that hold components state and DOM.

      ...depending on the type of a React element the framework needs to perform different activities. ...Each React element is converted into a Fiber node of corresponding type that describes the work that needs to be done.

   4, You can think of a fiber as a datastructure that represents some ork to do or, in other words, a unit of work. Fiber's architecture also provides a convenient way to track, schedule, pause and abort the work.
   
   5, When a React element is converted into a fiber node for the first time, React uses the data from element to create a fiber in the `ReactFiber.js::createFiberFrom*()` function. IN the consequenct updates React reuses the fiber node and just updates the necessary properties using data from a corresponding React element. React may also need to move the node in based on the `key` prop or delete it if the corresponding React element is no longer returned from the `render` method.

      Check out the ReactChildFiber functions to see the list of all activities and corresponding ufnctions React performs for existing fiber nodes.

   6, Current and Work-in-progress tree

      ...One of React's core principles is consistency. React always updates the DOM in one go -- it doesn't show partial results. The `workInProgress` tree serves as a "draft" that's not visible to the user, so that React can process all components first, and then flush their changes to the screen.

   7, Side-effects

      ...most state and props upates will lead to side-effects. And since applying effects is a type of work, a fiber node is convenient mechanism to track effects in addition to updates. Each fiber node can have effects associated with it. They are encoded in the `effectTag` field.

      	  Fiber type is defined in `ReactInternalTypes::Fiber`, and effect fields are:
	  	nextEffect,
		firstEffect,
		lastEffect,
		etc.

      8, Each of React application has one or more DOM elements that act as containers.
      	 React create a fiber root object for each of those containers.

	 See `ReactFiberRoot`

      9, Q: Given a DOM element, how to retrieve react info attached to it?
      	 A: through those `__react*` properties. e.g. after retrieving a DOM node (i.e. through `querySelector()`)in Chrome, the following properties are available.

	    * __reactFiber
	    * __reactProps
	    * __reactEvents
	    

	 
      10, Fiber `tag` is used in the reconciliation algorithm to determine what work needs to be done. See `ReactWorkTags.js` for the full list.

      11, Reconciliation Algorithm:
      	  React performs work in two main phases: `render` and `commit`.

	  During the first `render` phase, React applies updates to components scheduled through `setState` or `React.render`, and figure out what needs to be updated in the UI. If it's the initial rneder, React creates a new fiber node for each element returned from the `render` method. In the following updates, fibers for existing React elements are reused and updated. **The result of the phase is a stree of fiber nodes marked with side-effects**. The effects describe the work that needs to be done during hte following `commit` phase. During this second phase, React takes a fiber tree marked with effects and applies whtme to instances. It goeso ver the list of effects and performs DOM updates and other changes visible to a user.

	  It's important to understand that the work during the firset `render` phase can be performed **asynchronously**. ...work pauses are made possible by the fact that the work performed during this phase doesn't lead to any user-visible changes, like DOM updates. In contrast, the following `commit` phase is is always **synchronous**. This is because the work performed during this state leads to changes visible to the user, e.g. DOM updates. That's why React needs to do them in a single pass.

	  Calling lifecycle methods is one type of work performed by React. Some methods are called during the `render` phase and others during the `commit` phase.

	  ...`render` phase doesn't produce side-effects like DOM updates, so that React can process updates to components asynchronously.

	  All fiber nodes are processed in the work loop. The `nextUnitOfWork` holds a reference to the fiber node form the `workInProgress` tree that has some work to do. Four main functions are used to traverse the tree and initiate or complete the work:

	  - performUnitOfWork
	  - beginWork
	  - completeUnitOfWork
	  - completeWork

	  ...The function `beginWork` always returns a pointer to the next child to process in the loop or `null`. ...Once the node is completed, it'll need to perform work for siblings and backtrack to the parent after that. ...Only once all branches starting with child nodes are completed does it complete the work for the parent node and backtracks.


	  As for `commit` phase, wne it starts, React has 2 trees and the effects list. The first tree represents the state currently rendered on screen. Then there's an alternate tree built during the `render` phase. It's called `finishedWork` or `workInProgress` in the sources and represents the state that needs to be refrlected on the screen. This alternate tree is linked similarly to the current tree through the `child` and `sibling` pointers.

	  As for the effects list: it's the *result* of running the `render` phase. The whole point of rendering was to determine which nodes need to be inserted, updated, or deleted, and which compoentns need to have their lifecycle methods called. And that's what the effect list tell us. And it's exatly the set of nodes that's interated during the commit phase.

	  
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
   C-x TAB runs command indent-rigidly, which is an interactive native compiled Lisp that indents all lines starting in the region.

8, 
