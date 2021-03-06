# redux-saga example

Usually splitting your app state into `pages` feels natural, but sometimes you'll want to have global state for your app. This is an example using `redux` and `redux-saga` that works with universal rendering. This is just one way it can be done. If you have any suggestions or feedback please submit an issue or PR.

> This example and documentation is based on the [with-redux](https://github.com/zeit/next.js/tree/master/examples/with-redux) example.

## Deploy your own

Deploy the example using [ZEIT Now](https://zeit.co/now):

[![Deploy with ZEIT Now](https://zeit.co/button)](https://zeit.co/new/project?template=https://github.com/zeit/next.js/tree/canary/examples/with-redux-saga)

## How to use

### Using `create-next-app`

Execute [`create-next-app`](https://github.com/zeit/next.js/tree/canary/packages/create-next-app) with [npm](https://docs.npmjs.com/cli/init) or [Yarn](https://yarnpkg.com/lang/en/docs/cli/create/) to bootstrap the example:

```bash
npm init next-app --example with-redux-saga with-redux-saga-app
# or
yarn create next-app --example with-redux-saga with-redux-saga-app
```

Install it and run:

```bash
npm install
npm run dev
# or
yarn
yarn dev
```

Deploy it to the cloud with [now](https://zeit.co/now) ([download](https://zeit.co/download)):

```bash
now
```

## Notes
Un all comment in  next.config.js


Our page is located at `pages/index.js` so it will map the route `/`. To get the initial data for rendering we are implementing the static method `getInitialProps`, initializing the redux store and dispatching the required actions until we are ready to return the initial state to be rendered. Since the component is wrapped with `next-redux-wrapper`, the component is automatically connected to Redux and wrapped with `react-redux Provider`, that allows us to access redux state immediately and send the store down to children components so they can access to the state when required.

For safety it is recommended to wrap all pages, no matter if they use Redux or not, so that you should not care about it anymore in all child components.

`withRedux` function accepts `makeStore` as first argument, all other arguments are internally passed to `react-redux connect()` function. `makeStore` function will receive initialState as one argument and should return a new instance of redux store each time when called, no memoization needed here. See the [full example](https://github.com/kirill-konshin/next-redux-wrapper#usage) in the Next Redux Wrapper repository. And there's another package [next-connect-redux](https://github.com/huzidaha/next-connect-redux) available with similar features.


```js
import withRedux from 'next-redux-wrapper'
import nextReduxSaga from 'next-redux-saga'
import configureStore from './store'

export function withReduxSaga(BaseComponent) {
  return withRedux(configureStore)(nextReduxSaga(BaseComponent))
}

/**
 * Usage:
 *
 * class Page extends Component {
 *   // implementation
 * }
 *
 * export default withReduxSaga(Page)
 */
```

If you need to pass `react-redux` connect args to your page, you could use the following helper instead:

```js
import withRedux from 'next-redux-wrapper'
import nextReduxSaga from 'next-redux-saga'
import configureStore from './store'

export function withReduxSaga(...connectArgs) {
  return BaseComponent =>
    withRedux(configureStore, ...connectArgs)(nextReduxSaga(BaseComponent))
}

/**
 * Usage:
 *
 * class Page extends Component {
 *   // implementation
 * }
 *
 * export default withReduxSaga(state => state)(Page)
 */
```

Since `redux-saga` is like a separate thread in your application, we need to tell the server to END the running saga when all asynchronous actions are complete. This is automatically handled for you by wrapping your components in `next-redux-saga`. To illustrate this, `pages/index.js` loads placeholder JSON data on the server from [https://jsonplaceholder.typicode.com/users](https://jsonplaceholder.typicode.com/users). If you refresh `pages/other.js`, the placeholder JSON data will **NOT** be loaded on the server, however, the saga is running on the client. When you click _Navigate_, you will be taken to `pages/index.js` and the placeholder JSON data will be fetched from the client. The placeholder JSON data will only be fetched **once** from the client or the server.

After introducing `redux-saga` there was too much code in `store.js`. For simplicity and readability, the actions, reducers, sagas, and store creators have been split into seperate files: `actions.js`, `reducer.js`, `saga.js`, `store.js`
