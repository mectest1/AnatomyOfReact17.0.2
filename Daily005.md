# Daily005: Let's dive into code and debug

1, Set the env up and test a sample component:
   ```js
   function CounterComponent(props) {
   	    const {initCounter = 0 } = props;
	    const [counter, updateCounter] = useState(initCounter);

	    const addCounter = () => {
	    	  updateCounter(counter + 1);
	    };

	    return (
	    	   <div>
			<button onClick={addCounter}>Click Me</button>
			<span style={{'paddingLeft': '2em'}}>{counter}</span>
		   </div>
	    );
   }
   ```

2, Breakpoints to check:
   ReactDOM.
	render*
	commit*


3, Q: What happens when the button is clicked?
   A:


   //
   Related components:
   ReactDOM.

	renderWithHooks
	updateFunctionComponent
	beginWork
	performUnitOfWork
	workLoopSync
	renderRootSync
	performSyncWorkOnRoot


	updateDOMProperties
	updateProperties
	commitUpdate
	commitWork
	commitMutationEffects	//<------------------------
	invokeGuardedCallback
	commitRootImpl //React
	commitRoot
	performSyncWorkOnRoot

   1, RenderDOM.renderWithHooks():
      //line 15557:
      var children = Component(props, secondArg); // Check if there was a render phase update

   2, RenderDOM.performSyncWorkOnRoot()
      

      //line 23242:
      commitRoot(root);


   3, ReactDOM.commitMutationEffects():

      //line 24378:
      case Update:
      {
	var _current3 = nextEffect.alternate;
	commitWork(_current3, nextEffect);
	break;
      }


   4, 


4, Q: Where is the DOM property updated? // Start from the end
   A: ReactDOM.updateProperties(): line 9532
      	     .updateDOMProperties()

      These methods are invoked by
      	    commitMutationEffects()
	    ...
	    commitROot()
	    performSyncWorkOnRoot()

      //
      ReactDOM.commitMutationEffects():
	//line 24378:
	case Update:
	     var _current3 = nextEffect.alternate
	     commitWOrk(_current3, nextEffect);

        //line 24393:
	nextEffect = nextEffect.nextEffect;	//<-------------

      //
      Full Call Stack:
      setTextContent():
	//line 2227:
	if (firstChil && firstChild === node.lastChild && firstChild.nodeType === TEXT_NODE) {
	   firstChil.nodeValue = text
	   return;
	}
      updateDOMProperties()
	//line 9113:
	for (var i = 0; i < updatePayload.length; i += 2) {
    	    var propKey = updatePayload[i];
	    var propValue = updatePayload[i + 1];

	//line 9121:
	if (propKey === CHILDREN) {
	   setTextContent(domElement, propValue);
	}
	
      updateproperties()
	domElement: <span> DOM Node
	updatePayload: ['children', '1']
	tag: 'span'
	lastRawProps,
	nextRawProps: {children: 0, style: {paddingLeft: '2em'}}

	//line 9543:
	updateDOMProperties(domElement, updatePayload, wasCustomComponentTag, isCustomComponentTag);

	
      commitUpdate()
	updateFiberProps(domElement, newProps);
	updaetProperties(domElement, updatePayload, type, oldProps, newProps)
	
      commitWork (react-dom.v1.development.js:22314)
        //line 22299:
	var instance = finishedWork.stateNode;	//instance: <span> DOM node

	var newProps = finishedWork.memoizedProps; // newProps:

	var updatePayload = finishedWork.updateQueue; //['children', '1]
	
	//line 22314:
	commitUpdate(instance, updatePayload, type, oldProps, newProps);
	  instance: <span> DOM node
	  updatePayload: ['children', '1']
	  type: 'span'
	  oldProps: {children: 0, style: {paddingLeft: '2em'}}
	  newProps: {children: 0, style: {paddingLeft: '2em'}}
      commitMutationEffects (react-dom.v1.development.js:24381)
      callCallback (react-dom.v1.development.js:3942)
      invokeGuardedCallbackDev (react-dom.v1.development.js:3991)
      invokeGuardedCallback (react-dom.v1.development.js:4053)
      commitRootImpl (react-dom.v1.development.js:24093)
      unstable_runWithPriority (react.v1.development.js:2864)
      runWithPriority$1 (react-dom.v1.development.js:11521)
      commitRoot (react-dom.v1.development.js:23954)
      performSyncWorkOnRoot (react-dom.v1.development.js:23242)
      (anonymous) (react-dom.v1.development.js:11572)
      unstable_runWithPriority (react.v1.development.js:2864)
      runWithPriority$1 (react-dom.v1.development.js:11521)
      flushSyncCallbackQueueImpl (react-dom.v1.development.js:11567)
      flushSyncCallbackQueue (react-dom.v1.development.js:11554)
      discreteUpdates$1 (react-dom.v1.development.js:23346)
      discreteUpdates (react-dom.v1.development.js:3753)
      dispatchDiscreteEvent (react-dom.v1.development.js:6064)
	
	

5, Comment from ReactDOM.setTextContent():

   > Set the text content property of a node. For text updates, it's faster to set the `nodeValue` of the Text node directly instead of using `.textContent` which will remove the existing node and create a new one.

   Q: If this "remove the existing node and create new behavior" defined in DOM Specification, or merely implementation detail of the web browser?
   
      Might need to
      - Check the DOM speficition, or
      - Check the Chromium/Firefox source code to find out.

   Q2: How big is the impact on performance?


6, Q: What else is the work done in ReactDOM.commitRootImpl()?
   A: See ReactDOM.commitRootImpl():
      //line 24063:
      invokeGuardedCallback(null, commitBeforeMutationEffects, null);	//commitBeforeMutationEffects
      //line 24093:
      invokeGuardedCallback(null, commitMutationEffects, null, root, renderPriorityLevel); //commitMutationEffects
      //line 24123:
      invokeGuardedCallback(null, commitLayoutEffects, null, root, lanes); //commitLayoutEffects
      				  -> commitLifeCycles()
      //line 24233:
      onCommitRoot(finishedWork.stateNode, renderPriorityLevel);


7, Q: What happens when the setState() method is called? -- Or, what exactly is the setState() method under the hood?
   A: It is ReactDOM.dispatchAction()

      When the button is clicked, ReactDOM.dispatchDiscreteEvent(): line 6059 is called to trigger th 'click' event.

      See ReactDOM.createEventListenerWrapperWithPriority() for more info.


      //Call Stack:
      ReactDOM.dispatchAction(fiber, queue, action)
          fiber: FiberNode {type: CounterComponent, ...}

	  //line 16639:
	  update: {
	  	  lane: 1,
		  action: 2,	//the new value for state
	  }

	  //line 16714:
	  scheduleUpdateOnFiber(fiber, lane, eventTime);
      addCounter (AppTest.jsx:69)
      callCallback (react-dom.v1.development.js:3942)
      invokeGuardedCallbackDev (react-dom.v1.development.js:3991)
      invokeGuardedCallback (react-dom.v1.development.js:4053)
      invokeGuardedCallbackAndCatchFirstError (react-dom.v1.development.js:4067)
      executeDispatch (react-dom.v1.development.js:8418)
      processDispatchQueueItemsInOrder (react-dom.v1.development.js:8450)
      processDispatchQueue (react-dom.v1.development.js:8463)
      dispatchEventsForPlugins (react-dom.v1.development.js:8474)
      (anonymous) (react-dom.v1.development.js:8683)
      batchedEventUpdates$1 (react-dom.v1.development.js:23322)
      batchedEventUpdates (react-dom.v1.development.js:3742)
      dispatchEventForPluginEventSystem (react-dom.v1.development.js:8682)
      attemptToDispatchEvent (react-dom.v1.development.js:6180)
      dispatchEvent (react-dom.v1.development.js:6099)
      unstable_runWithPriority (react.v1.development.js:2864)
      runWithPriority$1 (react-dom.v1.development.js:11521)
      discreteUpdates$1 (react-dom.v1.development.js:23339)	//<------------------------
      discreteUpdates (react-dom.v1.development.js:3753)
      dispatchDiscreteEvent (react-dom.v1.development.js:6064)
          dispatchEvent: <div#container>
	  domEventName: 'click'
	  eventSystemFlags: 0
	  nativeEvent: PointEvent{}
      
      

      //Call Stack 2:
      renderRootSync (react-dom.v1.development.js:23600)
      performSyncWorkOnRoot (react-dom.v1.development.js:23206)
      (anonymous) (react-dom.v1.development.js:11572)
      unstable_runWithPriority (react.v1.development.js:2864)
      runWithPriority$1 (react-dom.v1.development.js:11521)
      flushSyncCallbackQueueImpl (react-dom.v1.development.js:11567)
      flushSyncCallbackQueue (react-dom.v1.development.js:11554)
      discreteUpdates$1 (react-dom.v1.development.js:23346)
          //line 23339:
	  return runWithPriority$1(UserBlockingPriority$2, fn.bind(null, a, b, c, d));
	  	 //
		 fn: dispatchEvent
	  
	  //line 23346:
	  flushSyncCallbackQueue(); //< ---------------------------------------------------
      discreteUpdates (react-dom.v1.development.js:3753)
      dispatchDiscreteEvent (react-dom.v1.development.js:6064)
      
      
      //===========================
      Summary:
      Key functions:
      1, ReactDOM.dispatchAction()
      2, ReactDOM.discreteUpdate$1()
		fn: dispatchEvent ->... dispatchAction
		flushSyncCallbackQueue() ->... renderRootSync()
      3, ReactDOM.dispatchDiscreteEvent()

8, Q: Where does the return value of useState() come from?
   A: It comes from ReactDOM.updateReducer()

      //Call Stack:
      updateReducer (react-dom.v1.development.js:15807)
          //line 15795:
	  var hook = updateWorkInProgressHook(); //<--------------------------------------
	      //updateWorkInProgressHook() is where the `cached` hook value comes from

	  //line 15919:
	  return [hook.memoizedState, dispatch];
	  	 hook.memoizedState: hmm, cached value
		 dispatch: bound dispatchAction
      updateState (react-dom.v1.development.js:16216)
      useState (react-dom.v1.development.js:17021)
      useState (react.v1.development.js:1598)
      CounterComponent (AppTest.jsx:66)
      renderWithHooks (react-dom.v1.development.js:15557)
      updateFunctionComponent (react-dom.v1.development.js:17935)
      beginWork (react-dom.v1.development.js:19906)
      beginWork$1 (react-dom.v1.development.js:24969)
      performUnitOfWork (react-dom.v1.development.js:23740)
      workLoopSync (react-dom.v1.development.js:23659)
      renderRootSync (react-dom.v1.development.js:23618)
      performSyncWorkOnRoot (react-dom.v1.development.js:23206)
      (anonymous) (react-dom.v1.development.js:11572)
      unstable_runWithPriority (react.v1.development.js:2864)
      runWithPriority$1 (react-dom.v1.development.js:11521)
      flushSyncCallbackQueueImpl (react-dom.v1.development.js:11567)
      flushSyncCallbackQueue (react-dom.v1.development.js:11554)
      discreteUpdates$1 (react-dom.v1.development.js:23346)
      discreteUpdates (react-dom.v1.development.js:3753)
      dispatchDiscreteEvent (react-dom.v1.development.js:6064)


      //=======================================================
      Summary: Key fucntions
      1, ReactDOM.updateReducer()
      2, ReactDom.updateWorkInProgressHook()


9, Wrap-up for today:
   1, Render CallStack:
      //
      renderWithHooks
      updateFunctionComponent
      beginWork
      performUnitOfWork
      workLoopSync
      renderRootSync //<----------------------------
      performSyncWorkOnRoot

   2, Commit CallStack:
      //
      updateDOMProperties
      updateProperties
      commitUpdate
      commitWork
      commitMutationEffects //<--------------------
      commitROotImpl //React
      commitRoot
      performSyncWorkOnRoot
      
   
   3, for useState:
      updateWorkInProgressHook
      updateReducer

   
   4, for setState (the returned callback from useState):
      dispatchAction
      discreteUdpate$1
	fn: dispatchEvent ->... dispatchAction
	flushSyncCallbackQueue() ->... performSyncWorkOnRoot
      dispatchDiscreteEvent	      
   5,
   


10, Next: try to break things, etc.
    1, try to use hook out of React Component
    2, 