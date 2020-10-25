- **Dummy** objects are passed around but never actually used. Usually they are just used to fill parameter lists.
- **Fake** objects actually have working implementations, but usually take some shortcut which makes them not suitable for production (an in memory database is a good example).
- **Stubs** provide canned answers to calls made during the test, usually not responding at all to anything outside what's programmed in for the test. Stubs may also record information about calls, such as an email gateway stub that remembers the messages it sent, or maybe only how many messages it sent.
- **Mocks** objects pre-programmed with expectations which form a specification of the calls they are expected to receive.

### Lifecycle

Test lifecycle with stubs:

1. Setup - Prepare object that is being tested and its stubs collaborators.
2. Exercise - Test the functionality.
3. Verify state - Use asserts to check object's state.
4. Teardown - Clean up resources.

Test lifecycle with mocks:

1. Setup data - Prepare object that is being tested.
2. **Setup expectations** - Prepare expectations in mock that is being used by primary object.
3. Exercise - Test the functionality.
4. **Verify expectations** - Verify that correct methods has been invoked in mock.
5. Verify state - Use asserts to check object's state.
6. Teardown - Clean up resources.

### Summary

Both mocks and stubs testing give an answer for the question: **What is the result?**

Testing with **mocks** are also interested in: **How the result has been achieved?**