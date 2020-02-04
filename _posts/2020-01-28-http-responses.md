---
layout: post
title: Formating RESTful API Responses
permalink: /formatting-restful-api-responses/
categories:
  - http
  - RESTful API
  - JavaScript
  - Python
---

Helpers/middlewares to formats API responses into stuctured and human-readable way (especially for **frontend** dev)

## Available language (at the moment..)
- [Express Node.js](https://gist.github.com/fadziljusri/b2a1110c1281563e4b47201ba8d3d51c#file-httpresponses-js)
- [Python](https://gist.github.com/fadziljusri/b2a1110c1281563e4b47201ba8d3d51c#file-http_responses-py)


## Response format (json)
```js
// 2xx success response
{
  "status": "success",
  "message": "",
  "data": "<any>"
}

// 4xx error response
{
  "status": "failed",
  "statusText": "",
  "message": "",
  "data": "<any>"
}
```

## Example in Express Node.js
```js
const { Res200 } = require("./httpResponses")

// sending 200 response 
router.get("/", (req, res, next) => {
    ...
    let data = { something: "returned" };
    return Res200(
        res, 
        data, 
        "(Optional) Your response message or 200 default 'Ok'"
    );
});

// 200 response output
doFetchingSomething().then(res=>{
    /**
    'res.data' output
    {
        "status": "success",
        "message": "(Optional) Your response message or 200 default 'Ok'",
        "data": { something: "returned" }
    }
    */
})
```

## More Response Type???
Check the codes from given files, its a helper functions/middlewares **NOT** a library. It is readable and you can modify the responses with your coding preference styles


## [Github Gist](https://gist.github.com/fadziljusri/b2a1110c1281563e4b47201ba8d3d51c)
