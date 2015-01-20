---
layout: post
title: "Mermaid: Javascript based sequence diagrams and graphs"
date: 2015-01-19 23:35:00 -0500
comments: true
categories:
---
[Mermaid](https://github.com/knsv/mermaid) is a nice javascript library to generate sequence diagrams, flowcharts and graphs from markdown-like-syntax. It is based on [d3.js](http://d3js.org/).

It also has a command line tool, but there is currently no option to style the output with that tool.

I wanted to use it internally for personal projects along with some CSS styling so I threw together a small webpage using angular that generates mermaid diagrams from it's syntax. The webapp is located [here](http://www.naseer.in/mermaid-webapp/) ([source](https://github.com/naseer/mermaid-webapp))

Right now, I typically type out the syntax and just take a screenshot with `⌘+Shift+4` to embed it in my documents - though I hope this can be improved with proper sharing features in the future.
