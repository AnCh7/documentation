#### Mock Service Worker

Mock Service Worker (MSW) is a seamless REST/GraphQL API mocking library for browser and Node.js.

Utilizing the [Service Worker API](https://developer.mozilla.org/en-US/docs/Web/API/Service_Worker_API), which can intercept requests for the purpose of caching, Mock Service  Worker responds to captured requests with your mock definition on the  network level. This way your application knows nothing about the  mocking.

In Node.js MSW implements a [low-level interception algorithm](https://github.com/mswjs/interceptors) that can utilize the very same request handlers you have for the browser. 

```js
// test/Dashboard.test.js

import React from 'react'
import { rest } from 'msw'
import { setupServer } from 'msw/node'
import { render, screen, waitFor } from '@testing-library/react'
import Dashboard from '../src/components/Dashboard'

const server = setupServer(
  // Describe network behavior with request handlers.
  // Tip: move the handlers into their own module and
  // import it across your browser and Node.js setups!
  rest.get('/posts', (req, res, ctx) => {
    return res(
      ctx.json([
        {
          id: 'f8dd058f-9006-4174-8d49-e3086bc39c21',
          title: `Avoid Nesting When You're Testing`,
        },
        {
          id: '8ac96078-6434-4959-80ed-cc834e7fef61',
          title: `How I Built A Modern Website In 2021`,
        },
      ]),
    )
  }),
)

// Enable request interception.
beforeAll(() => server.listen())

// Reset handlers so that each test could alter them
// without affecting other, unrelated tests.
afterEach(() => server.resetHandlers())

// Don't forget to clean up afterwards.
afterAll(() => server.close())

it('displays the list of recent posts', async () => {
  render(<Dashboard />)

  // 🕗 Wait for the posts request to be finished.
  await waitFor(() => {
    expect(
      screen.getByLabelText('Fetching latest posts...'),
    ).not.toBeInTheDocument()
  })

  // ✅ Assert that the correct posts have loaded.
  expect(
    screen.getByRole('link', { name: /Avoid Nesting When You're Testing/ }),
  ).toBeVisible()

  expect(
    screen.getByRole('link', { name: /How I Built A Modern Website In 2021/ }),
  ).toBeVisible()
})
```

