* Daily002: Introduce to React


1, Q: How does component like <App> get translated into react code?
   For example: what happens when the following code get executed?
   
   ```
      ReactDOM.render(
        <App />,
        document.getElementById('container')
      );
   ```
   
   A: It will get translated by Babel.js into the following code before being executed in web browser:

   ```
   ReactDOM.render(
      React.createElement(App, null),
      document.getElementById('container')
   );
   ```

   Related Components:
   1, babel.js::run(): line 61531:
      //line 61529:
      
      ```
      var scriptEl = document.createElement('script');
      scriptEl.text = transformCode(transformFn, script);
      headEl.appendChild(scriptEl);
      ```

2, Q: What is the call stack from ReactDOM.render() to custom component?
   A: Add a breakpoint in your custom component and the following call stack shows up:

   ```
   NameComponent (AppTest.jsx:36) // Custom Component
	...
   renderWithHooks (ReactDOM14.js:853)
   mountIndeterminateComponent (ReactDOM17.js:644)
   beginWork (ReactDOM18.js:1073)
   beginWork$1 (ReactDOM23.js:699)
   performUnitOfWork (ReactDOM22.js:580)
   workLoopSync (ReactDOM22.js:499)
   renderRootSync (ReactDOM22.js:458)
   performSyncWorkOnRoot (ReactDOM22.js:46)
   scheduleUpdateOnFiber (ReactDOM21.js:651)
   updateContainer (ReactDOM25.js:147)
   (anonymous) (ReactDOM25.js:751)
   unbatchedUpdates (ReactDOM22.js:197)
   legacyRenderSubtreeIntoContainer (ReactDOM25.js:750)
   render (ReactDOM25.js:833)
   ...
   ```

   Please ignore the number after ReactDOM at this moment.


   //
   In Summary, here is the bird's eye view of how React works:
   1, Babel translates `<CustomComponent>` into call to `React.createElement(CustomComponent, ...)`
   2, `ReactDom.render()` calls individual components throough work loop eventually.
   3, 


3, Introduce a sample App.js for local testing:

   ```
   function NameComponent() {
    const [name, updateName] = React.useState('');

    React.useEffect(() => {
        // React and ReactDOM resides in global namespace;
        // Object.keys(window).forEach((globalObj) => console.log(globalObj));
        console.log(window);
    }, []);

    React.useEffect(() => {
        console.log(`===================New name: ${name}`);
    }, [name]);

    const onNameChange = (e) => {
        const newName = e.target.value;
        // console.log(newName);
        updateName(newName);
    };

    return (
        <div>
            <form>
                <input type="text" onChange={onNameChange}/>
            </form>
            <div>Greetings, {name}</div>
        </div>
        
    );
   }

   const App = () => (
    <div>
        <NameComponent />
        <div>Hello, World!</div>
    </div>
   );
   ```



4, Q: What happens when typing contnet into the name field?
   A: NameComponent will be checked by the call stack tree twice:
      First time as a whole, and 
      Second time with useEffect() callback being invoked.

      Call Stack 1:

      ```
      NameComponent (AppTest.jsx:36)
      renderWithHooks (ReactDOM14.js:853)
      updateFunctionComponent (ReactDOM17.js:141)
      beginWork (ReactDOM18.js:1087)
      beginWork$1 (ReactDOM23.js:699)
      performUnitOfWork (ReactDOM22.js:580)
      workLoopSync (ReactDOM22.js:499)
      renderRootSync (ReactDOM22.js:458)
      performSyncWorkOnRoot (ReactDOM22.js:46)
      (anonymous) (ReactDOM10.js:1009)
      unstable_runWithPriority (React02.js:766)
      runWithPriority$1 (ReactDOM10.js:958)
      flushSyncCallbackQueueImpl (ReactDOM10.js:1004)
      flushSyncCallbackQueue (ReactDOM10.js:991)
      flushPendingDiscreteUpdates (ReactDOM22.js:138)
      flushDiscreteUpdates (ReactDOM22.js:119)
      finishEventHandler (ReactDOM03.js:418)
      batchedEventUpdates (ReactDOM03.js:452)
      dispatchEventForPluginEventSystem (ReactDOM08.js:229)
      attemptToDispatchEvent (ReactDOM05.js:789)
      dispatchEvent (ReactDOM05.js:708)
      unstable_runWithPriority (React02.js:766)
      runWithPriority$1 (ReactDOM10.js:958)
      discreteUpdates$1 (ReactDOM22.js:179)
      discreteUpdates (ReactDOM03.js:460)
      dispatchDiscreteEvent (ReactDOM05.js:673)
      ```


      Call Stack 2:

      ```
      (anonymous) (AppTest.jsx:43)
      invokePassiveEffectCreate (ReactDOM23.js:205)
      callCallback (ReactDOM03.js:649)
      invokeGuardedCallbackDev (ReactDOM03.js:698)
      invokeGuardedCallback (ReactDOM03.js:760)
      flushPassiveEffectsImpl (ReactDOM23.js:296)
      unstable_runWithPriority (React02.js:766)
      runWithPriority$1 (ReactDOM10.js:958)
      flushPassiveEffects (ReactDOM23.js:165)
      flushDiscreteUpdates (ReactDOM22.js:122)
      finishEventHandler (ReactDOM03.js:418)
      batchedEventUpdates (ReactDOM03.js:452)
      dispatchEventForPluginEventSystem (ReactDOM08.js:229)
      attemptToDispatchEvent (ReactDOM05.js:789)
      dispatchEvent (ReactDOM05.js:708)
      unstable_runWithPriority (React02.js:766)
      runWithPriority$1 (ReactDOM10.js:958)
      discreteUpdates$1 (ReactDOM22.js:179)
      discreteUpdates (ReactDOM03.js:460)
      dispatchDiscreteEvent (ReactDOM05.js:673)
      ```


      //
      Related Components:
      1, ReactDOM.renderWithooks()
      2, ReactDOM.beginWork()
      3, ReactDOm.dispatchDiscreteEvent()

5, Q: What are these React/ReactDOM methods for? Or, what are they trying to achieve?

   to answer that question,
   1, tutorials or docs should be checked first.
   2, Also, I need to re-write the source code React.js to and ReactDOM for my understanding;
   3, --and for that to happen, I need to break React.js and ReactDOM.js into multiple parts;
      This could be achieved by splitting the original source file into multiple files, each with around 1,000 lines;
      UMD wrapper could be stripped brutally;
      Major issues: problems caused by name conflicts, which could be resolved fairly quickly;
   4, 

6, Key takeaways today:
   1, Indent text blokc in Emacs: C-M-\
   2, Tutorial from JSer: React Soruce Code Walkthrough:
      ref: <https://github.com/JSerZANP/react-source-code-walkthrough-en/tree/main>
      ref2: <https://jser.dev/series/react-source-code-walkthrough>
      ref3: <https://jser.dev/>

   3, Topic: React Fiber Architecture
      ref: <https://github.com/acdlite/react-fiber-architecture>
      ref2: <https://blog.logrocket.com/deep-dive-react-fiber/>
      ref3: <https://blog.ag-grid.com/inside-fiber-an-in-depth-overview-of-the-new-reconciliation-algorithm-in-react/>
      ref4: <https://legacy.reactjs.org/docs/codebase-overview.html>

   4, 

7, 
