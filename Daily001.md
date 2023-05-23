# Daily 001: Get things running in local env

1, Check out source code of React 17.0.2 from official repository

   ref: <https://github.com/facebook/react/tree/17.0.2>

2, Follow the **Development Workflow** of [How to Contribute](https://legacy.reactjs.org/docs/how-to-contribute.html) guide to build React

   ref: <https://legacy.reactjs.org/docs/how-to-contribute.html>

3, Install local web server and access dev.html in web browser for testing.

   For example, to install pushstate-server:

   `npm install -g pushstate-server`

   And to run pushstate-server:

   `pushstate-server -d public/`

   Open the following address in web browser and try to open dev.html with following URL:

   http://0.0.0.0:9000/dev.html

4, You might also need to download babel.js into local env to avoid retrieveing it remotely for local testing.



