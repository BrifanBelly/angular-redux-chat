<p align="center">
  <img src="src/assets/images/logos/Angular2ReduxChatHeaderImage.png" alt="Angular 2 Redux Chat" width="810" height="405"/>
</p>

> An Angular 2 chat app using [Angular 2](https://angular.io/), [Redux](https://github.com/reactjs/redux), [Angular CLI](https://github.com/angular/angular-cli), [Webpack](https://webpack.github.io/), [TypeScript](http://www.typescriptlang.org/), Services, Injectables, [Karma](http://karma-runner.github.io/), Forms, [SCSS](http://sass-lang.com/), and [tslint](http://palantir.github.io/tslint/) by the [ng-book 2 team](https://ng-book.com/2)

This repo shows an example chat application using Redux and Angular 2. The goal is to show how to use the Redux data architecture pattern within Angular 2, using the core Redux library. It also features:

* A step-by-step tutorial on how to write a [minimal Redux clone in Typescript](minimal/tutorial)
* How to write a [minimal counter app with Redux and Angular 2](minimal)
* Webpack configuration with TypeScript, Karma, SCSS, and tslint
* How to write injectable services in Angular 2
* How to separate presentational vs. container components
* Using action creators
* How to compose reducers
* And much more

<p align="center">
  <img src="src/assets/images/readme/full-chat-preview.png" alt="Angular 2 Redux Chat" width="800" height="577"/>
</p>


## Quick start



# run
npm start
```

Then visit [http://localhost:4200](http://localhost:4200) in your browser.

## Architecture

The app has three models:

* [`Message`](src/app/message/message.model.ts) - holds individual chat messages
* [`Thread`](src/app/thread/thread.model.ts) - holds metadata for a group of `Message`s
* [`User`](src/app/user/user.model.ts) - holds data about an individual user

<p align="center">
  <img src="src/assets/images/readme/redux-chat-models.png" alt="Model Diagram" width="500" height="119"/>
</p>

There are two reducers:

* [`ThreadsReducer`](src/app/thread/threads.reducer.ts) - manages the `Thread`s and their `Message`s
* [`UsersReducer`](src/app/user/users.reducer.ts) - manages the current `User`

There are also three top-level components:

* [`ChatNavBar`](src/app/chat-nav-bar/chat-nav-bar.component.ts) - for the top navigation bar and unread messages count
* [`ChatThreads`](src/app/chat-threads/chat-threads.component.ts) - for our clickable list of threads
* [`ChatWindow`](src/app/chat-window/chat-window.component.ts) - where we hold our current conversation

<p align="center">
  <img src="src/assets/images/readme/redux-chat-top-level-components.png" alt="Angular 2 Redux Chat" width="500" height="360"/>
</p>

## Components Subscribe to the Store

In this project, we're using the [official Redux library](https://github.com/reactjs/redux) instead of a wrapper or Redux-inspired spin-off. At the top of our app we create a new Redux Store and [provide it to the dependency injection system](src/app/app.ts#L55). This let's us inject it into our components.

Our [container components](https://medium.com/@dan_abramov/smart-and-dumb-components-7ca2f9a7c7d0#.a2dspl7hn) inject the Redux Store and subscribe to any changes. Consider this excerpt from the nav-bar which keeps the count of unread messages:

```typescript
export default class ChatNavBar  {
  unreadMessagesCount: number;

  constructor(@Inject(AppStore) private store: Store<AppState>) {
    store.subscribe(() => this.updateState());
    this.updateState();
  }

  updateState() {
    // getUnreadMessagesCount is a selector function
    this.unreadMessagesCount = getUnreadMessagesCount(this.store.getState());
  }
}
```

You can see that in the constructor we inject our `Store` (which is typed to `AppState`). We immediately subscribe to any changes in the store. This callback will not be called unless an action is dispatched to the store, so we need to make sure we load the initial data. To do this, we call `this.updateState()` one time after the subscription.

`updateState` reads the state from the store (`this.store.getState()`) and then calls the _selector function_ `getUnreadMessagesCount` (you can [find the implementation of that here](src/app/reducers/ThreadsReducer.ts#L138)). `getUnreadMessagesCount` calculates the number of unread messages. We then take that value and set it to `this.unreadMessagesCount`. Because `unreadMessagesCount` is an instance variable which appears in the template, Angular will rerender this component when the value changes.

This pattern is used throughout the app.

## State

The top-level state has two keys: `users` and `threads`:

```typescript
interface AppState {
  users: UsersState;
  threads: ThreadsState;
}

interface UsersState {
  currentUser: User;
};

export interface ThreadsEntities {
  [id: string]: Thread;
}

export interface ThreadsState {
  ids: string[];
  entities: ThreadsEntities;
  currentThreadId?: string;
};
```

ThreadsState stores the list of Threads indexed by id in `entities`, as well as a complete list of the ids in `ids`.

We also store the id of the current thread so that we know what the user is currently looking at - this is valuable for the unread messages count, for instance.

In this app, we store the Messages in their respective Thread and we don't store the Messages apart from that Thread. In your app you may find it useful to separate Messages into their own Messages reducer and keep only a list of Message ids in your Threads.

Here's a screenshot using [Redux Devtools](https://github.com/gaearon/redux-devtools) of the initial state:

<p align="center">
  <img src="src/assets/images/readme/redux-chat-initial-state.png" alt="Angular 2 Redux Chat State Tree" width="800" />
</p>


## Bots

This app implements a few simple chat bots. For instance:

* Echo bot
* Reversing bot
* Waiting bot

<img src="src/assets/images/readme/redux-chat-echo-bot.png" alt="Angular 2 Redux Chat Bots" width="346" height="348"/>

<div style="clear:both"></div>


## Detailed Installation

**Step 1: Install Node.js from the [Node Website](http://nodejs.org/).**

We recommend Node version 4.1 or above. You can check your node version by running this:

```bash
$ node -v
vv4.1...
```

**Step 2: Install Dependencies**

```bash
npm install
```

## Running the App

```bash
npm start
```

Then visit [http://localhost:4200](http://localhost:4200) in your browser.

## Running the Tests

You can run the unit tests with:

```bash
npm run test
```

## Build Redux in TypeScript Tutorial

This repository contains a step-by-step tutorial on how to build a minimal-redux store in Typescript. You can read a [blog post explaining this code here](#). You can also find the code in [`minimal/tutorial`](minimal/tutorial). The final result looks like this (with or without Observables):

<p align="center">
  <img src="src/assets/images/readme/minimal-redux-ts.png" alt="Minimal Redux in TypeScript" width="800"/>
</p>

## Minimal Angular 2 Redux Integration

This repository also contains an example of a minimal integration of Redux with Angular 2 to build a counter app. You can also read about how to build this project [here at the ng-book blog](#).

<p align="center">
  <img src="src/assets/images/readme/working-counter-app.png" alt="Minimal Redux and Angular 2 Counter" width="800"/>
</p>





## License
 [MIT](/LICENSE.md)
