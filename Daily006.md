# Daily007: Try to break React, i.e. let React throw errors


1, Video: Link Clark - A Cartoon intro into Fiber

   Concepts:
	HostRoot
	Cooperative Scheduling

2, Use hook outside of a React Component

   1, It does not necessarily break React, LOL.
      
   2, Q: Where is the validation/restrictions done on hooks and other React features?
      Is it done in ESLint, built script, or React internal validation?
      

   3, 
   
   

3, Scenario: on page initialization:

   //Call Stack:
   createEventListenerWrapperWithPriority (react-dom.v1.development.js:6056)
   addTrappedEventListener (react-dom.v1.development.js:8555)
   listenToNativeEvent (react-dom.v1.development.js:8549)
   (anonymous) (react-dom.v1.development.js:8504)
   listenToAllSupportedEvents (react-dom.v1.development.js:8499)
       //line 8499:
       allNativeEvents.forEach(function (domEventName) {
        if (!nonDelegatedEvents.has(domEventName)) {
          listenToNativeEvent(domEventName, false, rootContainerElement, null);
        }

        listenToNativeEvent(domEventName, true, rootContainerElement, null);
      });
   createRootImpl (react-dom.v1.development.js:26959)
   ReactDOMBlockingRoot (react-dom.v1.development.js:26907)
   createLegacyRoot (react-dom.v1.development.js:26993)
   legacyCreateRootFromDOMContainer (react-dom.v1.development.js:27090)
   legacyRenderSubtreeIntoContainer (react-dom.v1.development.js:27116)
   render (react-dom.v1.development.js:27212)

   //Call Stack 2:
   renderWithHooks (react-dom.v1.development.js:15519)
   mountIndeterminateComponent (react-dom.v1.development.js:18438)
   beginWork (react-dom.v1.development.js:19892)
   beginWork$1 (react-dom.v1.development.js:24969)
   performUnitOfWork (react-dom.v1.development.js:23740)
   workLoopSync (react-dom.v1.development.js:23659)
   renderRootSync (react-dom.v1.development.js:23618)
   performSyncWorkOnRoot (react-dom.v1.development.js:23206)
   scheduleUpdateOnFiber (react-dom.v1.development.js:22794)
   updateContainer (react-dom.v1.development.js:26526)
   (anonymous) (react-dom.v1.development.js:27130)
   unbatchedUpdates (react-dom.v1.development.js:23357)
   legacyRenderSubtreeIntoContainer (react-dom.v1.development.js:27129)
       //line 27116:
       root = container._reactRootContainer = legacyCreateRootFromDOMContainer(container, forceHydrate);
       
       //line 27129:
       unbatchedUpdates(function () {
        updateContainer(children, fiberRoot, parentComponent, callback);
      });
   render (react-dom.v1.development.js:27212)



4, Q: Why the name `Fiber`?

   A: Thread consists of fibers. Since JavaScript VM is single threaded, novel way is needed to implement this multi-thread-alike feature, hence fiber.

   Q: Why the name `Host`?

   A: React is independent of rendering platform. The `Host` term means the ultimate end result that shall be rendered to UI: be it DOM Node, or Native View component (I guess -- in which case, wouldn't `Native` be a better fit? e.g. `NativeRoot`, `NativeComponent`, etc. Funny thing is `ReactNative` was raised to be different from the default `React[DOM]`, hence the name `Host`).


5, Article by Eric Churchill: The React Source COde: A Beginner's Walkthrough
   ref: <https://medium.com/@ericchurchill/the-react-source-code-a-beginners-walkthrough-i-7240e86f3030>

   //
   This writing style is the one I adopted thirteen years ago, and should be avoided now.
   
6, Next:
   1, More articles & videos;
   2, Unit Tests;

 