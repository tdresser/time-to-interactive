# PerformanceNavigationTiming interactive

Web developers require more information on page load performance in the wild. For many page loads, the primary page content is displayed long before the user can actually interact with the page. `PerformanceNavigationTiming`'s `interactive` attribute will return a `DOMHighResTimeStamp` with a time value approximating the first time the user can interact with the page and the page's primary content has been displayed on the screen. `interactive` will be undefined until page load is complete. (TODO - when is page load complete?)

We plan to rely on [`firstContentfulPaint`](https://github.com/tdresser/time-to-first-meaningful-paint/blob/master/README.md) or [DomContentLoaded](https://html.spec.whatwg.org/multipage/indices.html#event-domcontentloaded) to determine when the page's primary content has been displayed on the screen. We'll choose between these based on further experimentation.

## Using `interactive` 
`interactive` will be added to the [PerformanceNavigationTiming](https://www.w3.org/TR/navigation-timing-2/#sec-PerformanceNavigationTiming) interface in the [Navigation Timing API](https://www.w3.org/TR/navigation-timing-2/).

```javascript
window.TODO = () => { 
  var navigationTiming = performance.getEntriesByType("navigation")[0];
  console.log("Time to Interactive: " + navigationTiming.interactive);
}
```

## Computation
PerformanceNavigationTiming's `interactive` attribute is computed based on  [`firstContentfulPaint`](https://github.com/tdresser/time-to-first-meaningful-paint/blob/master/README.md) or [DomContentLoaded](https://html.spec.whatwg.org/multipage/indices.html#event-domcontentloaded), and the set of time windows during which the page is considered interactive.

### Interactivity 
We approximate page interactivity based on how busy the thread executing JavaScript is. We consider the page interactive during any time window of length > 10 seconds for which the thread executing JavaScript contains no tasks longer than 50ms. This means that, if input shows up at any time during this time window, it will be a maximum of 50ms before event handlers for this event are executed.

### Combining interactivity and when the page is first displayed

If the page is first displayed during a window of interactivity, `interactive` = when the page was first displayed. Otherwise, `interactive` = the start of the first window of interactivity after the page was first displayed. 

## TODO
* Is 50ms a reasonable task length threshold?
* Is 10 seconds a reasonable window length threshold?
* Is `interactive` a reasonable name?
* Define when page load is complete.
