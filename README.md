--[[
    timer.luau
    Simple timer utility for Roblox
    @author      @XiK_Z
    @license     MIT
    @within      timer
    @description
        A lightweight timer module for Roblox Luau, supporting start, stop, and configurable intervals.
]]

export type Timer = {
	time: number,
	from: number,
	to: number,
	tick: number,
	totalTime: number,
	running: boolean,
	alias: string,

	describe: (self: Timer, from: number, to: number, tick: number) -> (),
	start: (self: Timer, Callback: (() -> ())?) -> (),
	reset: (self: Timer) -> (),
	resetTo: (self: Timer, to: number) -> (),
	getTime: (self: Timer) -> number,
	getTotalTime: (self: Timer) -> number,
	getAlias: (self: Timer) -> string,
	getTick: (self: Timer) -> number,
	getFrom: (self: Timer) -> number,
	getTo: (self: Timer) -> number,
	getRunning: (self: Timer) -> boolean,
	getTimeLeft: (self: Timer) -> number,
	getTimePassed: (self: Timer) -> number,
	getTimePassedPercent: (self: Timer) -> number,
	getTimeLeftPercent: (self: Timer) -> number,
	edit: (self: Timer, from: number, to: number, tick: number) -> (),
	stop: (self: Timer) -> (),
}

local timer = {}
timer.__index = timer

--[[
    Creates a new timer instance.
    @param alias string -- Optional name for the timer.
    @return timer -- The new timer object.
    @example
    ```lua
    local t = timer.new("MyTimer")
    ```
]]
function timer.new(alias : string)
	assert(typeof(alias) == "string", "alias must be a string")
	local self = setmetatable({}, timer)
	self.time = 0
	self.from = 0
	self.to = 0
	self.tick = 0
	self.totalTime = tick()
	self.running = false
	if not alias then alias = "Timer" end
	self.alias = alias
	return self
end

--[[
    Describes the timer's range and tick interval.
    @param from number -- Start time.
    @param to number -- End time.
    @param tick number -- Interval between ticks (default 1).
    @example
    ```lua
    t:describe(0, 10, 1)
    ```
]]
function timer:describe(from : number, to : number, tick : number)
	assert(typeof(from) == "number", "from must be a number")
	assert(typeof(to) == "number", "to must be a number")
	self.from = from
	self.to = to
	self.time = 0
	if not tick then tick = 1 end
	assert(typeof(tick) == "number", "tick must be a number")
	self.tick = tick
    return self
end

--[[
    Starts the timer. Increments time by tick until reaching 'to'.
    Optionally accepts a callback to run when the timer completes.
    @param Callback function? -- Function to call when timer finishes.
    @example
    ```lua
    t:start(function()
        print("Timer finished!")
    end)
    ```
]]
function timer:start(Callback : ((self : Timer) -> ()))
	if Callback then
		assert(typeof(Callback) == "function", "Callback must be a function")
	end
	assert(not self.running, string.format("%s is already running", self.alias))
	self.running = true
	self.time = self.from
	task.spawn(function()
		while self.running do
			if self.tick > self.time then
				warn(string.format("%s tick is greater than time", self.alias))
			end
			task.wait(self.tick)
			if self.time < self.to then
				self.time += self.tick
			else
				self:stop()
				if Callback then
					Callback(self)
				end
				self.time = self.to
			end
		end
	end)
    return self
end

--[[
    Resets the timer to the starting value.
    @example
    ```lua
    t:reset()
    ```
]]
function timer:reset()
	assert(self.running, string.format("%s is not running", self.alias))
	self.time = self.from
    return self
end

--[[
    Resets the timer to a specific value.
    @param to number -- The value to reset the timer to.
    @example
    ```lua
    t:resetTo(5)
    ```
]]
function timer:resetTo(to : number)
	assert(self.running, string.format("%s is not running", self.alias))
	assert(typeof(to) == "number", "to must be a number")
	self.time = to
    return self
end

--[[
    Gets the current timer value.
    @return number -- The current time.
    @example
    ```lua
    print(t:getTime())
    ```
]]
function timer:getTime()
	assert(self.running, string.format("%s is not running", self.alias))
	return self.time
end

--[[
    Gets the total time since the timer was created.
    @return number -- The total time.
    @example
    ```lua
    print(t:getTotalTime())
    ```
]]
function timer:getTotalTime()
	assert(self.running, string.format("%s is not running", self.alias))
	return self.totalTime
end

--[[
    Gets the timer's alias.
    @return string -- The alias.
    @example
    ```lua
    print(t:getAlias())
    ```
]]
function timer:getAlias()
	assert(self.running, string.format("%s is not running", self.alias))
	return self.alias
end

--[[
    Gets the tick interval.
    @return number -- The tick interval.
    @example
    ```lua
    print(t:getTick())
    ```
]]
function timer:getTick()
	assert(self.running, string.format("%s is not running", self.alias))
	return self.tick
end

--[[
    Gets the starting value.
    @return number -- The starting value.
    @example
    ```lua
    print(t:getFrom())
    ```
]]
function timer:getFrom()
	assert(self.running, string.format("%s is not running", self.alias))
	return self.from
end

--[[
    Gets the ending value.
    @return number -- The ending value.
    @example
    ```lua
    print(t:getTo())
    ```
]]
function timer:getTo()
	assert(self.running, string.format("%s is not running", self.alias))
	return self.to
end

--[[
    Checks if the timer is running.
    @return boolean -- True if running, false otherwise.
    @example
    ```lua
    print(t:getRunning())
    ```
]]
function timer:getRunning()
	assert(self.running, string.format("%s is not running", self.alias))
	return self.running
end

--[[
    Gets the time left until the timer completes.
    @return number -- Time left.
    @example
    ```lua
    print(t:getTimeLeft())
    ```
]]
function timer:getTimeLeft()
	assert(self.running, string.format("%s is not running", self.alias))
	return self.to - self.time
end

--[[
    Gets the time passed since the timer started.
    @return number -- Time passed.
    @example
    ```lua
    print(t:getTimePassed())
    ```
]]
function timer:getTimePassed()
	assert(self.running, string.format("%s is not running", self.alias))
	return self.time - self.from
end

--[[
    Gets the percent of time passed.
    @return number -- Percent of time passed (0-1).
    @example
    ```lua
    print(t:getTimePassedPercent())
    ```
]]
function timer:getTimePassedPercent()
	assert(self.running, string.format("%s is not running", self.alias))
	return (self.time - self.from) / (self.to - self.from)
end

--[[
    Gets the percent of time left.
    @return number -- Percent of time left (0-1).
    @example
    ```lua
    print(t:getTimeLeftPercent())
    ```
]]
function timer:getTimeLeftPercent()
	assert(self.running, string.format("%s is not running", self.alias))
	return (self.to - self.time) / (self.to - self.from)
end

--[[
    Edits the timer's range and tick interval while running.
    @param from number -- New start time.
    @param to number -- New end time.
    @param tick number -- New tick interval (default 1).
    @example
    ```lua
    t:edit(0, 20, 2)
    ```
]]
function timer:edit(from : number, to : number, tick : number)
	assert(self.running, string.format("%s is not running", self.alias))
	assert(typeof(from) == "number", "from must be a number")
	assert(typeof(to) == "number", "to must be a number")
	self.from = from
	self.to = to
	self.time = from
	if not tick then tick = 1 end
	assert(typeof(tick) == "number", "tick must be a number")
	self.tick = tick
    return self
end

--[[
    Stops the timer.
    @example
    ```lua
    t:stop()
    ```
]]
function timer:stop()
	assert(self.running, string.format("%s is not running", self.alias))
	self.running = false
    return self
end

return timer
