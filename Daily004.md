# Daily004: Checking Articles on React Fiber

1, Article by Max Koretskyi: The how and why on React's usage of inked list in Fiber

   ref: <https://medium.com/react-in-depth/the-how-and-why-on-reacts-usage-of-linked-list-in-fiber-67f1014d0eb7>

   

2, Test tip: `yarn test --watch TestName`

   If a debugger is needed, run `yarn debug-test --watch TestName`

   ref: .github/PULL_REQUEST_TEMPLATE.md

3, Article: Analyze runtime performance

   ref: <https://developer.chrome.com/docs/devtools/performance/?utm_source=devtools>

4, Article by Sebastian Markbage: Fiber Principles: Contributing to Fiber

   ref: <https://github.com/facebook/react/issues/7942>

   1, React issue tracker on github: <github.com/facebook/react/>

      Tag: Component: Reconciler

      ref: <https://github.com/facebook/react/labels/Component%3A%20Reconciler>

      2, 

5, Article by Max Koretsky: In-depth explanation of state and props update in React

   ref: <https://medium.com/react-in-depth/in-depth-explanation-of-state-and-props-update-in-react-51ab94563311>


6, Some sample callstacks by React:


   1, Error stack for invalid component property
   
    ...
    at assertValidProps (react-dom.v1.development.js:2716:15)
    at setInitialProperties (react-dom.v1.development.js:9309:5)
    at finalizeInitialChildren (react-dom.v1.development.js:10376:5)
    at completeWork (react-dom.v1.development.js:20316:19)
    at completeUnitOfWork (react-dom.v1.development.js:23779:18)
    at performUnitOfWork (react-dom.v1.development.js:23751:7)
    at workLoopSync (react-dom.v1.development.js:23659:7)
    at renderRootSync (react-dom.v1.development.js:23618:9)
    at performSyncWorkOnRoot (react-dom.v1.development.js:23206:20)
    at scheduleUpdateOnFiber (react-dom.v1.development.js:22794:9)

   2, Key components:

       ReactDOM.
		completeWork
		completeUnitOfWork
		performUnitOfWork
		workLoopSync
		renderRootSync
		performSyncWorkOnRoot
		scheduleUpdateOnFiber
		
		

   3, Warning stack:

        printWarning (react-dom.v1.development.js:61)
	error (react-dom.v1.development.js:37)
	warnHyphenatedStyleName (react-dom.v1.development.js:2441)
	warnValidStyle (react-dom.v1.development.js:2489)
	setValueForStyles (react-dom.v1.development.js:2562)
	setInitialDOMProperties (react-dom.v1.development.js:9074)
	setInitialProperties (react-dom.v1.development.js:9310)
	finalizeInitialChildren (react-dom.v1.development.js:10376)
	completeWork (react-dom.v1.development.js:20316)
	    //line 20316:
	    if (finalizeInitialChildren(instance, type, newProps, rootContainerInstance)) {
                markUpdate(workInProgress);
              }
	completeUnitOfWork (react-dom.v1.development.js:23779)
	    //line 23779:
	    next = completeWork(current, completedWork, subtreeRenderLanes); // Update render duration assuming we didn't error.
	performUnitOfWork (react-dom.v1.development.js:23751)
	workLoopSync (react-dom.v1.development.js:23659)
	renderRootSync (react-dom.v1.development.js:23618)
	performSyncWorkOnRoot (react-dom.v1.development.js:23206)
	scheduleUpdateOnFiber (react-dom.v1.development.js:22794)
	updateContainer (react-dom.v1.development.js:26526)
	(anonymous) (react-dom.v1.development.js:27130)
	unbatchedUpdates (react-dom.v1.development.js:23357)
	legacyRenderSubtreeIntoContainer (react-dom.v1.development.js:27129)
	render (react-dom.v1.development.js:27212)




   4, Key components:
      ReactDOM.
		renderRootSync()/renderRootConcurrent()
		commitRoot()/commitRootImpl()


   5, 

7, VanJS: The Smallest Reactive UI Framework in the World

   ref: <https://github.com/vanjs-org/van>

   1,  Related topics: Proxy, Reflect, etc.


8, Preact: 3kb React alternative

   ref: <https://preactjs.com/>

9,  Daily dose of tool: git tag

    Consider to use it for taking snapshots of projects releases.

10, Hmm, put read aside for a moment. Let's debug some code


