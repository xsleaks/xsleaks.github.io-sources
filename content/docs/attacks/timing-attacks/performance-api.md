+++
title = "Performance API"
description = ""
date = "2020-10-01"
category = [
    "Attack",
]
abuse = [
    "window.performance",
]
defenses = [
    "SameSite Cookies",
    "CORB",
]
menu = "main"
weight = 2
+++
# Performance API
The [`Performance API`](https://developer.mozilla.org/en-US/docs/Web/API/Performance) provides access to performance-related information enhanced by the data from the [`Resource Timing API`](https://developer.mozilla.org/en-US/docs/Web/API/Resource_Timing_API)  
This data can be accessed by using [`performance.getEntries`](https://developer.mozilla.org/en-US/docs/Web/API/Performance/getEntries) or [`performance.getEntriesByName`](https://developer.mozilla.org/en-US/docs/Web/API/Performance/getEntriesByName)  
It can also be used to get the execution time using the difference of [`performance.now()`](https://developer.mozilla.org/en-US/docs/Web/API/Performance/now) however this seems to be less precise.
# Resource Timing API
Using the [`Resource Timing API`](https://developer.mozilla.org/en-US/docs/Web/API/Resource_Timing_API) you can get the timings of network requests.  
The duration is provided for all requests but when theres a `Timing-Allow-Origin: *` header sent by the server the [`transfer size`](https://developer.mozilla.org/en-US/docs/Web/API/PerformanceResourceTiming/transferSize) and domain lookup time is also provided.
## Get duration of a network request
```javascript
async function ifCached(url) {
    // Using an image instead of fetch() as some requests had duration = 0
    let image = new Image().src = url;
    // Wait for request to be added to performance.getEntries();
    await new Promise(r => setTimeout(r, 1000));
    // Get last added timings
    let res = performance.getEntries().pop();
    delete image
    console.log("Request duration: " + res.duration);
    return res.duration
}
```