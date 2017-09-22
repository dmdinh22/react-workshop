# Step 13 - Action-Reducers

The goal of this step is to complete the factoring out of state logic from the top-level `App` that we started in [Step 12](../12-api-lib). Whenever the user performs an interaction, in `App` we first make an API call and then transform the state based upon the interaction. Borrowing terms from [Redux](http://redux.js.org/), the API call is an ["action"](http://redux.js.org/docs/basics/Actions.html) and the state transformation is a ["reducer"](http://redux.js.org/docs/basics/Reducers.html). By factoring out these "action-reducers" (without actually using Redux), we can allow the `App` component to just focus on the UI, and not data and logic.

The goal of these action-reducer is to perform an API call and then return a `Promise` that contains the updated `state`.

As always, if you run into trouble with the [tasks](#tasks) or [exercises](#exercises), you can take a peek at the final [source code](src/).

## Tasks

Take out the API call ("action") and the logic for updating `this.state.emails` ("reducer") in `_handleFormSubmit` of `App`. Create a new [`src/action-reducers/index.js`](src/action-reducers/index.js) and export an `addEmail()` function:

```js
import {
  addEmail as addEmailApi
} from '../api';

export const addEmail = (emails, newEmail) =>
  addEmailApi(newEmail).then(({success}) => {
    if (success) {
      return [
        {
          ...newEmail,
          id: Date.now(),
          date: `${new Date()}`,
          unread: true
        },
        ...emails
      ];
    }

    throw new Error('Unable to send email!');
  });
```

Now `_handleFormSubmit` in `App` should just call the new `addEmail` action-reducer and update `this.state.emails` with the value in the returned promise:

```js
// other imports

import {addEmail} from './action-reducers';

// helper components

export default class App extends PureComponent {
  // prop types & default props

  // initialize state

  // lifecycle methods

_handleFormSubmit(newEmail) {
    addEmail(this.state.emails, newEmail)
      // if the email was successfully updated, we have to make
      // a request to get the new list of emails, but we'll have
      // to wait for the response of that request, so let's add to
      // our state immediately and then later when the response
      // comes back, the server-side list will update. This is mainly
      // here to demonstrate immutable updating of data structures
      .then(emails => this.setState({emails, showForm: false}));
  }

  // other helper methods

  // render()
}
```

## Exercises

- Create the remaining action-reducers for `getEmails()`, `deleteEmail()`, `markRead()` & `markUnread()`

## Next

Go to [Step 14 - Redux-y Actions and Reducers](../14-reduxy-actions-reducers/).

## Resources

- [Redux](http://redux.js.org/)

## Questions

Got questions? Need further clarification? Feel free to post a question in [Ben Ilegbodu's AMA](http://www.benmvp.com/ama/)!