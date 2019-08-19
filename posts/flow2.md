---
title: 'Week 2: Basic Functionality'
description: Overview of week 2 on the Flow Homepage team
date: 2019-08-19
tags:
  - front-end
  - chrome extension
  - UX
layout: layouts/post.njk
---

This was an exciting week! It feels like more work got done without as much work required. We finished a minimum viable page with usable buttons and acceptable visuals.

![Flow Homepage Screenshot](/img/flow2screenshot.png)

## Tab Buttons

My responsibility this week mostly revolved around getting usable buttons to appear on the page. This was actually trivial from getting our Chrome extension to list out tab names from last week. Basically, instead of the content script updating the page with plain text, it creates link elements (with titles and links) and updates the page with those, instead. Everything else is simply styling, especially involving things we learned this week, like:

- Rounded edges
- Shadows
- Hover pseudo-selector styling
- HSL to maintain same color with different lightnesses on page
- Linear-gradient overlay to improve text readability on backgrounds
- Flex and grid to start creating card and widget feels

## Date and Time

Other members of my team worked on adding date/time functionality to the page. I'm a little less familiar with this aspect, but I know the basics. We basically used the momentum library to format the Javascript Date/Time objects into something pretty to output. We also set an interval to update the time on the page constantly.

Interestingly, this gave us a good lesson on importing libraries to JS when on the web. Options are limited here. Since it is hard or impossible to import a web-hosted JS file to your own JS file, your options are to:

1. Host the JS file locally and use the ES6 package manager (more on this below)
2. Simply import the web-hosted JS to HTML directly, trying your best to remember dependencies

We opted for option 2 and tried to use comments to make help remember dependencies. A quick note about the ES6 package manager. Basically, you can import and export packages and functionality between javascript files just like in node with only slightly different syntax. The important thing to remember is to add "type="module"" when importing the script to HTML.

I write that here for me to remember more than anything. 😅

## Weather

This is the functionality I'm most removed from this week. It's not in the screenshot, but our app is also able to display the local temperature. We opted to have a back-end that carries API calls to external sources for the front-end. This is so that we can maintain different API keys and handle that mess ourselves, but better solutions could present themselves later as we dive deeper into using external information.

But for now, this feature requires running our back-end as well as front-end.