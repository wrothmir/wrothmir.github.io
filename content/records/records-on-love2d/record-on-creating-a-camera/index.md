---
draft: true

title: 'Creating a Camera in LÖVE'
subtitle: ''
summary: 'A good camera makes a game feel much more polished. This record explores how to make one for 2d games.'
description: 'Understanding and coding what makes a Camera feel good.'

date: 2024-10-20T11:23:55-07:00
lastMod: 2024-10-20T11:23:55-07:00

author: "fenrir"
authorLink: "https://fenrir.is-a.dev"
license: "<a rel='license external nofollow noopener noreffer' href='https://opensource.org/licenses/GPL-3.0' target='_blank'>GPL-3.0</a>"

tags: ["löve", "game-dev", "lua"]
categories: ["deepdive"]
relative: true

featuredImage: "images/initial-world-and-camera-positions.png"
featuredImagePreview: "images/initial-world-and-camera-positions.png"

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

# Introduction

A camera is an important part of most games. A good camera often affects how the
player experiences the game. This record goes over how to create a good camera
in the LÖVE framework. If you are reading this, having a basic understanding of
programming and lua is recommended.

## Understanding How Cameras Work In Games

Before we set up our basic camera, we need to understand how a camera works in
games. At a first glance, it might seem that the camera is moving around the
world, but it isn't really so. The camera really doesn't exist. It is just what
we see in our screen that is changing. So the camera is really the window of our
game and it does not move at all - it is static. So how does what we see on the
screen change? If we can't move the camera, we simply have to move the game world!

Let us visualize what moving the game world really means. Say the world is
located at (x, y) = (0, 0). These are the coordinates of the origin of the world.
Initially, let us have our camera also located at this position.

![Initial World and Camera positions](./images/initial-world-and-camera-positions.png "Initial World and Camera Positions")

The viewport of our screen (or our camera) is fixed. The question is, how exactly
do we move the world to give the illusion that the camera is moving? We can
move the world in two directions:
1. In the direction that the camera wants to move to view the target.
2. Opposite to the direction that the camera wants to move to view the target.

If we move the world in the direction that we want the camera to move (the target),
we end up going away from the target. This is illustrated in the
image below.

![Moving the World in the direction we want the Camera to go](./images/moving-the-world-towards-the-target.png "Moving the World towards the target")

If we move the world in the opposite direction that we want the camera to move,
we end up going towards the target.

![Moving the World in away from the target](./images/moving-the-world-away-from-the-target.png "Moving the World away from the target")

Now that we know how to move the World around, we can start building our
camera.

## Setting Up a Basic Camera

Let's set up a simple rectangle in our LÖVE game that will act as the target.

```lua {title="main.lua"}
local love = require("love")

function love.load()
  player = {
    x = 100,
    y = 100,
    speed = 100
  }
end

function love.update(dt)
  if love.keyboard.isDown("left") then
    player.x = player.x - (player.speed * dt)
  elseif love.keyboard.isDown("right") then
    player.x = player.x + (player.speed * dt)
  end

  if love.keyboard.isDown("up") then
    player.y = player.y - (player.speed * dt)
  elseif love.keyboard.isDown("down") then
    player.y = player.y + (player.speed * dt)
  end
end

function love.draw()
  love.graphics.rectangle("fill", player.x, player.y, 30, 30)
end
```

If you run this program using the following command from the root directory of your
project, you will see a white rectangle in your screen that you can move around
using the arrow keys.

```bash
love .
```

If you, however, go out of the screen area, you lose sight of the rectangle. To
make sure that doesn't happen, we will setup a camera.

To keep track of how much the world needs to moved in the x and y coordinates,
we will create a Camera table that has two fields storing said x and y values.

```lua {title="The Camera table"}
local Camera = {
    x = 0,
    y = 0,
}
```

Assuming that we want the target to be in the center of the camera at all times,
we can calculate the distance of the target from the camera center and use that
to move the world.

![](./images/distance-to-move-to-center-the-target.png)

The units of x (or pixels) the world needs to be moved to get the target in the
center are
\[ x = x_t - cameraWidth/2 \]
\[ y = y_t - cameraHeight/2 \]

If the Camera's width and height are the same as that of the screen, we can
get those values in our `update` loop as follows:

```lua {title="Updating the Camera's x and y values"}
function love.update(dt)
  ...
  Camera.x = player.x - love.graphics.getPixelWidth()/2
  Camera.y = player.y - love.graphics.getPixelHeight()/2
end
```

Now we need to move the World by some the x and y values stored in the Camera
table during the `draw` loop.

```lua {title="Drawing the World with an offset from the Camera"}
function love.draw()
  love.graphics.push()
  love.graphics.translate(-Camera.x, -Camera.y)
  love.graphics.rectangel("fill", player.x, player.y, 30, 30)
  love.graphics.pop()
end
```

Let us have a look at each line of code in the `draw` loop.

Firstly, we have `love.graphics.push()` in the first line. This is a function
provided by LÖVE that pushes all the currently applied transformation to the
stack. This is necessary to make sure that any other transformations being
applied do not interfere with what we are going to draw next.

Next up is `love.graphics.translate(-Camera.x, -Camera.y)`. This translates
whatever we draw after this line by `-Camera.x` and `-Camera.y`.

Now we draw our player. And then pop the transformations we pushed to the stack
using `love.graphics.pop()` making sure the previous transformations are applied
again.

If we run this code now, we see that the target does not move at all when
we use our arrow keys. But what is really happening is that the target is now
set to always be in the center of the Camera. And since the Camera's width and
height were set to be equal to the screen, the screen center is now the Camera
center.

We can make a box to indicate our world boundaries to see if the camera is
indeed moving.

```lua {title="Adding World boundaries"}
function love.draw()
  love.graphics.push()
  love.graphics.translate(-Camera.x, -Camera.y)
  love.graphics.rectangle(
    "line",
    0,
    0,
    love.graphics.getPixelWidth(),
    love.graphics.getPixelHeight()
  )
  love.graphics.rectangel("fill", player.x, player.y, 30, 30)
  love.graphics.pop()
end
```

Now when we run the code, we can see that the target stays in the center of the
Camera and is actually moving around the World!

--TODO: Add image/gif here.

We now have our basic Camera setup!

## Reorganizing Code to Make a Library

If we want to make our Camera more modular, we will have to move it to a new
folder and create a library. I personally prefer doing this to keep the main
game loop clean and easy to read.

First we move the Camera table to a new file `camera/init.lua`. Once that is
done, we need to create a few helper methods.

```lua {title="The Camera library"}
local love = require("love")

local Camera = { }
Camera.__index = Camera

function Camera:new()
  local o = setmetatable({}, Camera)
  o._x = 0
  o._y = 0
  o.width = love.graphics.getPixelWidth()
  o.height = love.graphics.getPixelHeight()
  o.target = nil
  return o
end

function Camera:attach(target)
  self.target = target
end

function Camera:update()
  self._x = self.target.x - self.width/2
  self._y = self.target.y - self.height/2
end

function Camera:set()
  love.graphics.push()
  love.graphics.translate(-self._x, -self._y)
end

function Camera:unset()
  love.graphics.pop()
end

return Camera
```

Camera is now a class which has a few fields and methods. You could choose not
to create a class but I prefer to and so I've made it into one.

We create a new Camera instance by using the `Camera:new()` method.
`Camera.target` now holds a reference to the target that we want to track, and the
target has to be manually attached once.

The `Camera:update()` method has the logic for calculating the distance to
move the world to get the target in the center.

`Camera:set()` and `Camera:unset()` can now be used to apply transforms, draw
our sprites, and then unset the transformations.

## Implementing Damping

Damping, simply put, is the smooth movement of the camera instead of jerky motions.
In physics, damping is the loss of energy of an oscillating system. Here, we
will initially model it as a linear system, but also take a look at oscillating
systems later.

### Linear Interpolation

To dampen our camera movement each update, we can use the calculated difference
between the target and the camera center, subtract the camera position to get the
delta (the distance between the target and the camera center), multiply it with
a damping factor, and then add it to our current camera coordinates.

\[x = x + ( (x_t - cameraWidth/2) - x ) * dampingFactor\]
\[y = y + ( (y_t - cameraWidth/2) - y ) * dampingFactor\]

This is similar to the equation for Linear Interpolation:

\[y = x + (a - x) * b \]
where b is the interpolation factor, a is the destination, and x and y are the
current position and new position respectively.

Here is the updated code for the `Camera:update()` function to utilize this
interpolation.

```lua {title="Linear Interpolation Damping"}
function Camera:new()
  ...
  o.damping = 0.1
  return o
end

function Camera:update()
  self._x = self._x + ( (self.target.x - self.width/2) - self._x ) * self.damping
  self._y = self._y + ( (self.target.y - self.height/2) - self._y ) * self.damping
end
```

The closer the value of `damping` is to 1, the faster the camera
moves, and subsequently, the farther away the value is from 1, the slower it
moves. You can change the value of the damping factor to try out what feels
good for your game.

This current setup however, is not frame-independent, which means that on a
machine with a faster frame rate, the camera will reach its destination faster.
To make the damping equation factor this in, we can use delta time (`dt`) which
is the time passed between two consecutive frames.

Multiplying this along with the damping factor makes our update function
frame-independent. At higher frame rates, `dt` is smaller and so is the product
of `damping` and `dt`, and lower frame rates `dt` is larger, which keeps the
damping consistent over time.

![Linear Interpolation](./images/lerp.png "Linear Interpolation Function")

As a consequence of this multiplication, we need to increase the value of
`damping` to make sure that the product does not become too small.

```lua {title="Linear Interpolation Damping (Frame Independent)"}
function Camera:new()
  ...
  o.damping = 4
  return o
end

function Camera:update(dt)
  self._x = self._x + ( (self.target.x - self.width/2) - self._x ) * self.damping * dt
  self._y = self._y + ( (self.target.y - self.height/2) - self._y ) * self.damping * dt
end
```

Of course, you can go further and have different damping values for both the axes
if you feel like it!

What we just implemented was damping using *Linear Interpolation*, more commonly
known as *lerp*. There are many more ways you can rig the camera to dampen
movement. Let's have a look at some more of them.

### Exponential Decay Damping

In this approach, the camera initially moves at higher speeds and then slows
down exponentially as it reaches the target location. It is similar to lerp,
but instead of multiplying directly by damping, we multiply the delta by
a negative exponent of damping subtracted from 1.

\[ y = x + (b - x) * (1 - e^{-x}) \]

![Exponential Decay](./images/exp_decay.png "Exponential Decay Function (exaggerated)")

To make this frame-independent, we multiply `damping` by `dt`.

```lua {title="Exponential Decay Damping (Frame Independent)"}
function Camera:update(dt)
  self._x = self._x + ( (self.target.x - self.width/2) - self._x ) * (1 - math.exp(self.damping * dt))
  self._y = self._y + ( (self.target.y - self.height/2) - self._y ) * (1 - math.exp(self.damping * dt))
end
```

We have a couple of problems here though.

1. First up, as the distance gets smaller, the steps we take to reach the target
become infinitesimally small, so small that the camera may appear to be jerky
between steps.

2. Secondly, unless the delta reaches exactly zero, which is unlikely, the calculation
will go on forever even if we stop moving our character, at times crossing zero
and becoming negative.

To fix this, we can set a threshold that enforces a limit on how small the
steps can be.

```lua {title="Threshold-Capped Exponential Decay Damping (Frame Independent)"}
function Camera:new()
  ...
  o.damping = 4
  o.damping_threshold= 5
  return o
end

function Camera:update(dt)
  local x = self.target.x - self.width/2
  local y = self.target.y - self.height/2

  local delta_x = x - self._x
  local delta_y = y - self._y

  local factor = 1 - math.exp(-self.damping * dt)

  if (delta_x * factor) < self.damping_threshold then
    self._x = x
  else
    self._x = self._x + delta_x * factor
  end

  if (delta_y * factor) < self.damping_threshold then
    self._y = y
  else
    self._y = self._y + delta_y * factor
  end
end
```

With these improvements, we avoid unnecessary calculation and increase performance.
You can change the curve of damping by messing around with the factor to get the
desired smoothness.
