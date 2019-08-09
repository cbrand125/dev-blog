---
title: 'Week 1: Proof of Concept'
description: Overview of week 1 on the Flow Homepage team
date: 2019-08-09
tags:
  - planning
  - chrome extension
layout: layouts/post.njk
---

This week we organized our workflows and tried to come up with some basic proof-of-concepts (mostly involving a Chrome extension). For organization, we created a lot of resources on git and referred to a lot of Agile concepts. Discussions got a little heated, but ultimately we came to some good conclusions about code lifecycle.

## Organization
Basically, our repo is split into 3 sub-repos: client, server, and documents. These categories are well-understood between us, so this wasn't too much of a discussion.

We settled on using a kanban board for our weekly sprints. One of the first things we did was come up with some user stories for some overarching features we want for the site. We turned these into issues so they wouldn't be lost and could easily be added to project boards. But our first week sprint got a little more specific with tasks like "AADev, I want to have protected branches so nobody can push to master." We assigned and moved these specific and organization tasks through to make sure we didn't forget about anything and ensured progress.

![Flow KanBan Board](/img/kanban.png)

Finally, we decided on a code lifecycle. For now, things should work in this order:
1. Create your own branch
2. Develop your feature
3. Pull request for your branch
4. Go through code review as long as needed
5. Get approval from two different team members
6. Dean merges code to master

There was heated discussion over Dean being the only one to merge to master. Basically, I feel extremely strongly that the developer of code should be the one to merge and resolve conflicts. After a lot of thinking, we came to the conclusion that this issue is fixed with a 'production' branch. Basically, we will follow the above process except the developer will merge to the production branch for step 6. Then, at certain times, when we guarentee production is left alone for a period of time, we will test it. Finally, Dean can make a 'cut' of production and merge it into master to form a release.

So that's our plan for now. It seems to be working well, it seems like it will continue to work well, but we are also ready to change at a moment's notice if we need to. 😊

## Proof of Concept
The other half of what we did was plan ahead and make sure a lot of our grand plans would be possible. This included:
  - API Access
  - The process by which we communication with so many APIs
  - Having users log onto their accounts on other sites (possibly through ours)
  - Accessing tab management

We split this up and all researched different aspects of these. I was tasked with making a proof-of-concept for tab management. To my dismay, I quickly realized that the APIs the browsers have for this purpose actually only work within the context of an extension. Not to be deterred, I got to work on making a quick extension to use for our purposes. First, I made a generic tutorial extension using [Google's guide](https://developer.chrome.com/extensions/getstarted). This taught me a lot, so then I got to work on using the chrome.sessions API to actually give us some tab management.

### CHROME EXTENSION MINI-TUTORIAL TIME
Okay, so, it took me a while, but eventually I understood how Chrome extensions work. Basically, they each run their own little invisible background.html. And that background.html has an accompanying background.js. And these two just run in the background forever with access to all the special chrome APIs.

Aside from that, you can do a few other things. You can make an action popup for the widget at the top right (click it and your own special tiny popup page). You can make your own options page for the extension. You can save things in a persistent storage for the extension. And finally, and most importantly for us, you can inject script into pages using content scripts!

### BACK TO FLOW
A core functionality of Chrome extensions is the ability to throw a content.js onto pages that meet conditions that you decide on. This content.js has access to some of the API, but not all of it. That's where it got a little messy. The content script does not have access to the sessions API, but we wanted to show session information on our own page.

Basically, we need some way for the background script to communicate with the content script so that session information can be passed along. I decided to use some messaging that the Chrome API has built in.

This would work as follows. First, the content.js would be injected and run some code. The code simply sends a message asking for the sessions:

```js
chrome.runtime.sendMessage({ greeting: 'hello' }, function(sessions) { /** do stuff when we hear back **/ });
```

Luckily, we have code to make sure that the background script is listening for this! And when she hears the call, she responds with the sessions:

```js
// Add a listener on the message recieved event
chrome.runtime.onMessage.addListener(function(request, sender, sendResponse) {
  // Send back the sessions object containing all recently closed sessions to whomever sent the message
  chrome.sessions.getRecentlyClosed(sessions => {
    sendResponse(sessions);
  });
  return true;
});
```

I learned that the return true has to be added for this function to work asynchronously, which like, it always is, because it's a listener, so I don't know what Google was thinking on that one. ANYWAY, finally, when content.js gets the response, the it's callback is executed:

```js
// When the background script responds with the sessions,
  // we will list out the recently closed tab/window names
  for (let i = 0; i < sessions.length; i++) {
    const page = sessions[i].tab || sessions[i].window;
    document.getElementById('recentTabs').innerHTML = '';
    document.getElementById('recentTabs').innerHTML += page.title + '<br />';
  }
```

And there you have it! The page has a list of all the recently closed tabs now.

SO, TO MAKE A LONG STORY SHORT, with a small extension we can provide comprehensive tab management within the Flow Homepage. 💖