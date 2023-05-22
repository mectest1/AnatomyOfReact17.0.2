Daily002: Introduce to React


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

2, 