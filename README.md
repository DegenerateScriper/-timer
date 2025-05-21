## 🧠 What Is This Module?
This is a timer module for Roblox. Imagine you want to run something after a delay, like showing a countdown, triggering a game event, or managing a round timer. This module lets you create a timer that counts up at regular intervals, and you can start, stop, reset, and even edit it while it runs.

## 🔧 How Do You Use It?
### 1. Require the Module
First, put timer.lua somewhere like ReplicatedStorage or ServerScriptService, then require it:

```lua
local Timer = require(game.ReplicatedStorage.timer)
```
### 2. Create a Timer Instance
To use the timer, you create one:

```lua
local myTimer = Timer.new("RoundTimer")
```
You can name it anything — this name is mostly for logging and organization. It doesn't affect functionality.

### 3. Set Up the Timer (Describe It)
Before the timer can run, you need to tell it what it should do. This is where you use :describe().

```lua
myTimer:describe(0, 10, 1)
```
This means:

Start at 0

Count up to 10

Increase by 1 every time

So every second (assuming 1 tick = 1 second), it will go:

```
0 → 1 → 2 → 3 → ... → 10
```
You can make it tick faster or slower by changing that last number.

### 4. Start the Timer
Now let’s make it go:

```lua
myTimer:start(function(timer)
	print(timer:getAlias() .. " finished!")
end)
```
What happens:

The timer starts at the from value (0).

It increases by tick every few seconds.

When it reaches to (10), it automatically stops.

If you gave it a function (like we did here), it runs that function at the end.

So this would print:

```nginx
RoundTimer finished!
```
You can leave out the function if you just want it to stop silently.

### 5. Stop the Timer Early
If you want to cancel the timer before it finishes:

```lua
myTimer:stop()
```
This stops it immediately. Useful if something else happens, like the player leaving or the game ending early.

### 6. Reset the Timer
You’ve got two ways to reset it:

:reset() – puts it back at the starting value (from).

:resetTo(x) – sets it to any number you want.

Example:

```lua
myTimer:resetTo(5) -- Sets it to 5 manually
```
You must call this while the timer is running or it will throw an error.

### 7. Check What’s Going On
Want to know how far along the timer is? These functions tell you:

```lua
myTimer:getTime() -- Current value
myTimer:getTimeLeft() -- How much is left
myTimer:getTimePassed() -- How much has passed
myTimer:getTimePassedPercent() -- 0 to 1
myTimer:getTimeLeftPercent() -- 0 to 1
myTimer:getRunning() -- Is it running?
```
You can use this for a GUI or debug printouts.

### 8. Change It While Running
Want to make the timer longer or faster after it starts? You can with :edit().

```lua
myTimer:edit(0, 20, 2) -- Now it counts to 20 in steps of 2
```
This restarts the timer with new settings.

### 9. Aliases Are Just Labels
Remember that "RoundTimer" name? That’s the alias. It doesn’t affect how the timer works — it's just a helpful label.

You can get it like this:

```lua
print(myTimer:getAlias()) -- "RoundTimer"
```
## 📌 Summary
To Use the Timer:
```lua
local Timer = require(...)          -- Get the module
local t = Timer.new("MyTimer")      -- Make one
t:describe(0, 5, 1)                 -- Set it to go 0→5 by 1s
t:start(function() print("Done!") end)  -- Start it
Optional:
t:stop() – Stop early

t:reset() – Start over

t:edit() – Change its behavior mid-run

t:getTime() – Track progress

t:getRunning() – Check if it’s going
```
