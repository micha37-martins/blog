---
title: "Open-WRT High Available - failover using keepalived"
date: 2024-01-15T11:30:03+00:00
weight: 1
# aliases: ["/first"]
tags: ["open-wrt", "keepalived", "ha"]
author: "Michael Martins"
# author: ["Me", "You"] # multiple authors
showToc: true
TocOpen: false
draft: false
hidemeta: false
comments: false
description: "Failover open-wrt router using keepalived."
disableHLJS: true # to disable highlightjs
disableShare: false
disableHLJS: false
hideSummary: false
searchHidden: true
ShowReadingTime: true
ShowBreadCrumbs: true
ShowPostNavLinks: true
ShowWordCount: true
ShowRssButtonInSectionTermList: true
UseHugoToc: true
cover:
    image: "<image path/url>" # image path/url
    alt: "<alt text>" # alt text
    caption: "<text>" # display caption under cover
    relative: false # when using page bundles set this to true
    hidden: true # only hide on current single page
editPost:
    URL: "https://github.com/<path_to_repo>/content"
    Text: "Suggest Changes" # edit text
    appendFilePath: true # to append file path to Edit link
---
![keepalive](keepalived.jpg)


## Motivation
Having some kind of homelab means you fiddle around with crucial infrastructure
of your home network. So chances are high to loose connection to internet for
your whole household. To avoid this...
