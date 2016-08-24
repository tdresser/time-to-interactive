# PerformanceNavigationTiming interactive

Web developers require more information on page load performance in the wild. For many page loads, the primary page content is displayed long before the user can actually interact with the page. PerformanceNavigationTiming's `interactive` attribute will return a `DOMHighResTimeStamp` with a time value approximating the first time when a page's primary content has been displayed on the screen and the user can interact with the page.

We rely on [`firstMeaningfulPaint`](https://github.com/tdresser/time-to-first-meaningful-paint/blob/master/README.md) to determine when the page's primary content has been displayed on the screen.

## Interactivity ##
We approximate page interactivity based on how busy the thread executing JavaScript is. We consider the page interactive during any time window of length > 10 seconds for which the thread executing JavaScript contains no tasks longer than 50ms. This means that, if input shows up at any time during this time window, it will be a maximum of 50ms before event handlers for this event are executed.

## Computation ##
PerformanceNavigationTiming's `interactive` attribute is computed based on [`firstMeaningfulPaint`](https://github.com/tdresser/time-to-first-meaningful-paint/blob/master/README.md), and the set of time windows during which the page is considered interactive.
```
var interactive
for each interactive_window, sorted by increasing start time:
  if interactive_window starts after firstMeaningfulPaint
    interactive = start of interactive_window
    break
  if interactive_window ends after firstMeaningfulPaint
    interactive = firstMeaningfulPaint
    break
```

## Web API ##
Time to interactive will be added to the [PerformanceNavigationTiming](https://www.w3.org/TR/navigation-timing-2/#sec-PerformanceNavigationTiming) interface in the [Navigation Timing API](https://www.w3.org/TR/navigation-timing-2/).

```javascript
window.onLoad = () => { 
  var navigationTiming = performance.getEntriesByType("navigation")[0];
  var interactive = navigationTiming.interactive;
  console.log("Time to Interactive: " + interactive);
}
```

