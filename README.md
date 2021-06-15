API
---

## Authentication

All endpoints require authentication. Authentication consists of two required HTTPS headers:
- `Authorization`: This is where your secret key goes.
- `X-User-ID`: This is where your Play.ht user ID goes.

These credentials are sent to you once you register for API access. If you haven't done that yet, you can contact us on `support [at] play.ht`.

Make sure to store your secret key privately and do not share it. Never use your secret key in the front-end part of your app or in the browser.
