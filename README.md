# consumer-utils

---
<a href="https://github.com/Exponential-Hosting/utils/issues"><img src="https://img.shields.io/github/issues/Exponential-Hosting/consumer-utils"></a>
<a href="https://github.com/Exponential-Hosting/utils/blob/main/LICENSE"><img src="https://img.shields.io/github/license/Exponential-Hosting/consumer-utils"></a>
<a href="https://twitter.com/intent/tweet?text=https%3A%2F%2Fgithub.com%2FExponential-Hosting%2Fconsumer-utils"><img src="https://img.shields.io/twitter/url?url=https%3A%2F%2Fgithub.com%2FExponential-Hosting%2Fconsumer-utils"></a>


## Quick Start:

### Get the API key
Login at https://www.exponentialhost.com/ and get your API key at https://www.exponentialhost.com/my-apps. Put your API key in EXPONENTIAL_API_KEY environment variable.

```bash
export EXPONENTIAL_API_KEY=<your-exponential-api-key>
```

### Installation:

```
npm install --save @exponential/consumer-utils
```

### Start calling the exponential APIs

```js
const exponential = require('@exponential/consumer-utils')(process.env.EXPONENTIAL_API_KEY);

exponential.call('website-screenshot-api', 'GET', '/', {
    params: {
        url: 'blog.exponentialhost.com'
    },
    responseType: 'stream'
});

exponential.call('tweet-image', 'POST', '/image', {
    headers: {
        "content-type": "application/json"
    },
    data: {
        "twitterUrl": "https://twitter.com/narendramodi/status/1571162212190007298",
        "imageType": "square",
        "templates": [
            "crisp"
        ]
    }
});

```

See below ([Examples](#Examples)) for more detailed examples

## API

### call(projectHandle, method, path, config)

Use the call function to call any exponential API as mentioned above. `exponential.call(projectHandle, method, path, config)`. `config` is an object containing optionally additional HTTP headers (`headers`), request body (`data`), URL params (`params`), and other optional axios configurations (https://github.com/axios/axios#request-config).

Response is an axios promise (https://github.com/axios/axios#response-schema).

### credits()

Get your remaining credits

```js
const exponential = require('@exponential/consumer-utils')(process.env.EXPONENTIAL_API_KEY);

console.log(exponential.credits); // Response: {credits_available: { freeCredits: <integer>, purchasedCredits: <integer> }, balanceCredits: <integer>, cumulativeTotalCredits: <integer>}
```

## Examples

### Example 1: Call the website screenshot API

```js
const exponential = require("@exponential/consumer-utils")(process.env.EXPONENTIAL_API_KEY);

const fs = require('fs');

async function callWebsiteScreenshotAPI() {
    const response = await exponential.call('website-screenshot-api', 'GET', '/', {
        params: {
            url: 'blog.exponentialhost.com'
        },
        responseType: 'stream'
    }).catch((e) => { 
        console.error(e);
    });
    response.data.pipe(fs.createWriteStream('/tmp/screenshot.png'));
    response.data.on('end', () => {
        console.log('screenshot written to /tmp/screenshot.png');
    });
}

callWebsiteScreenshotAPI();
```

### Example 2 : tweet image API 

```js
const exponential = require("@exponential/consumer-utils")(process.env.EXPONENTIAL_API_KEY);

exponential.call('tweet-image', 'POST', '/image', {
    headers: {
        "content-type": "application/json"
    },
    data: {
        "twitterUrl": "https://twitter.com/narendramodi/status/1571162212190007298",
        "imageType": "square",
        "templates": [
            "crisp"
        ]
    }
}).then((response) => { 
    console.log(response.data); // response { 'crisp': 'https://i.exponentialhost.com/tweetImages/crisp_1571162212190007298_square.png' }
}).catch((e) => { 
    console.error(e);
});
```