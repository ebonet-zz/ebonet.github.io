---
layout: post
title:  "Changing the URL without reloading the page"
category: quicktips
tag:
  - Javascript
  - Front End
---

I was working with a project that used a combination of server side rendering and updates via API. I wanted to have a behavior similar to SPA's, where the user clicks a button, data and url are changed without reloading the page, in a way that the url truly represents the state of the screen. To accomplish this, we can use the html5 history API:

``` coffeescript
update_url = (url,state)->
    window.history.pushState(state,"", url);
```

I am using coffeescript, but you would call window.history exactly the same way in JS. You can pass an object storing state variables, so that in case the user
presses the back button the previous screen can be restored properly.

To handle back presses, we answer to the popstate event:

``` coffeescript
$(window).on 'popstate', (event) ->
  if(event.originalEvent.state)
    updateUI(event.originalEvent.state)
```



[History API Docs](https://developer.mozilla.org/en-US/docs/Web/API/Window/history)
|
[CSSTricks Tutorial](https://css-tricks.com/using-the-html5-history-api/)
