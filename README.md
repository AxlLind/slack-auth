# simple-slack-verification
A simple express middleware for Slack signed secret authentication. See Slack's documentation [on signing verification](https://api.slack.com/docs/verifying-requests-from-slack).

If the request did not come from your Slack the middleware does not pass the request forward. On unauthorized requests it returns status 403 and the `unauthorizedResponse` object (see below).

## Installation
```
npm i simple-slack-verification
yarn add simple-slack-verification
```

## Usage
```js
var express = require('express');
var slackVerification = require('simple-slack-verification');

// with no options given
var app = express();
app.use( slackVerification() );
```

```js
var express = require('express');
var slackVerification = require('simple-slack-verification');

// with options
var app = express();
app.use(
  slackVerification({
    secret: "YOUR SLACK SECRET, PLEASE STORE SAFELY",
    unauthorizedResponse: {
      code: 'unauthorized',
      message: 'Unauthorized request'
    },
    maxSecondsOld: 60
  })
);
```

### Options
Passed in as an object to the `slackVerification` function with the following fields. All of them are optional and if omitted defaults to what's documented below:
- `secret`: Your personal [Slack secret](https://api.slack.com/docs/verifying-requests-from-slack#signing_secrets_admin_page). You can either give it in this parameter or just set it as the environment variable `SLACK_SIGNING_SECRET`. If this option is given the environment variable is not used.
  - Default: `process.env.SLACK_SIGNING_SECRET`.
  - Type: `String`
- `unauthorizedResponse`: Whatever is sent to the user when unable to verify signing.
  - Default: `{ code: 'unauthorized', text: 'Unable to verify Slack request' }`
  - Type: Anything [`res.send()` allows](https://expressjs.com/en/api.html#res.send).
- `maxSecondsOld`: The max age of the message you allow, in seconds.
  - Default: `300`, as per Slacks recommendation from their documentation.
  - Type: `Number`

## Security
Since this is a security-sensitive package here are some things done to increase security:
- A maximum age on requests to avoid replay attacks. This is a recommended step from Slacks documentation.
- Timing-safe comparisons of the signatures to avoid timing attacks. Also from Slacks documentation.
- See more from [Slack's documentation](https://api.slack.com/docs/verifying-requests-from-slack) on the security of the protocol.

If you want please review the code yourself and open an issue if you think something is questionable.
