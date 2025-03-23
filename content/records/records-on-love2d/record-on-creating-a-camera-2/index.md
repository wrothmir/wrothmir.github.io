---
draft: true

title: 'Record on Creating a Camera 2'
subtitle: ''
summary: ''
description: ''

date: 2024-12-02T09:46:35-08:00
lastMod: 2024-12-02T09:46:35-08:00

license: "<a rel='license external nofollow noopener noreffer' href='https://opensource.org/licenses/GPL-3.0' target='_blank'>GPL-3.0</a>"

tags: []
categories: []
series: ["Coding a Camera in LÖVE"]
series_weight: 2
seriesNavigation: true

relative: true

featuredImage: ""
featuredImagePreview: ""

images: [""]
toc:
  auto: true

hiddenFromHomePage: false
hiddenFromSearch: false
twemoji: false
lightgallery: true
ruby: true
fraction: true
linkToMarkdown: true
rssFullText: true
enableLastMod: true
enableWordCount: true
enableReadingTime: true
---

## Deadzones

Deadzones are, simply put, regions in which the target can move without the
camera moving to track it. Why are deadzones necessary? If you have the camera
tracking a target, readjusting the position at every small movement of the target
can become annoying for the player, sometimes even nauseating or disorienting.
This can be avoided by implementing deadzones.

![Camera with a Deadzone](./images/deadzone-1.png "Camera with a Deadzone")

To implement a deadzone, we need to know what area of the camera the target
is free to roam in. Having the ability to turn the deadzone on and off makes
the camera more robust, so lets add a field to keep track of that. The user
will also need a way to set these values once the object is instantiated.

```lua {title="Deadzone Table for the Camera"}
function Camera:new()
  ...

  o.deadzone = {
    enable = false,
    x = nil,
    y = nil,
    w = nil,
    h = nil,
  }

  ...
end

function Camera:setDeadzone(x, y, width, height)
  self.deadzone.enable = true
  self.deadzone.left_x = x
  self.deadzone.top_y = y
  self.deadzone.right_x = x + width
  self.deadzone.bottom_y = y + height
end
```

With this, we can now check if the target is in the deadzone or not and update
the world translation accordingly.

```lua {title="Guard for World translation update"}
function Camera:update(dt)
end
```

## Screen Shake
## Look-ahead

## Camera for non-target scenarios
