# Lua Cheatsheet for Game Development

## Table of Contents
- [Comments](#comments)
- [Variables and Data Types](#variables-and-data-types)
- [Basic Operations](#basic-operations)
- [Control Flow](#control-flow)
  - [If Statements](#if-statements)
  - [Loops](#loops)
- [Functions](#functions)
- [Tables (Arrays and Objects)](#tables-arrays-and-objects)
  - [Arrays (1-indexed!)](#arrays-1-indexed)
  - [Objects/Dictionaries](#objectsdictionaries)
- [String Operations](#string-operations)
- [Math Functions](#math-functions)
- [Game Programming Patterns](#game-programming-patterns)
  - [Game Object](#game-object)
  - [State Machine](#state-machine)
  - [Collision Detection](#collision-detection)
  - [Timer System](#timer-system)
- [Common Game Variables](#common-game-variables)
- [Useful Patterns](#useful-patterns)
  - [Clamp Function](#clamp-function)
  - [Distance Function](#distance-function)
  - [Linear Interpolation](#linear-interpolation)
  - [Table Utilities](#table-utilities)
- [Important Notes for Game Development](#important-notes-for-game-development)
- [PICO-8 Specific Notes](#pico-8-specific-notes)

## Comments
```lua
-- Single line comment

--[[
Multi-line comment
Can span multiple lines
]]
```

## Variables and Data Types
```lua
-- Numbers (all numbers are floats in Lua)
local x = 42
local pi = 3.14159

-- Strings
local name = "player"
local message = 'hello world'
local multiline = [[
This is a
multi-line string
]]

-- Booleans
local is_alive = true
local game_over = false

-- Tables (arrays and objects)
local inventory = {}
local player = {x = 0, y = 0, health = 100}

-- Nil (undefined/null)
local empty = nil
```

## Basic Operations
```lua
-- Math
local sum = 5 + 3
local diff = 10 - 4
local product = 6 * 7
local quotient = 15 / 3
local remainder = 17 % 5
local power = 2 ^ 3  -- 8

-- String concatenation
local greeting = "hello " .. "world"
local score_text = "score: " .. tostring(score)

-- Comparison
local equal = (a == b)
local not_equal = (a ~= b)  -- Note: ~= not !=
local greater = (a > b)
local less_equal = (a <= b)

-- Logical
local result = true and false  -- false
local result2 = true or false  -- true
local result3 = not true       -- false
```

## Control Flow

### If Statements
```lua
if player.health <= 0 then
    game_over = true
elseif player.health < 20 then
    print("low health!")
else
    print("healthy")
end

-- Short form
if game_over then return end
```

### Loops
```lua
-- For loop (numeric)
for i = 1, 10 do
    print(i)
end

for i = 10, 1, -1 do  -- countdown
    print(i)
end

-- For loop (iterate table)
local items = {"sword", "potion", "key"}
for index, item in ipairs(items) do
    print(index, item)
end

-- For loop (iterate key-value pairs)
local player = {x = 10, y = 20, name = "hero"}
for key, value in pairs(player) do
    print(key, value)
end

-- While loop
while player.health > 0 do
    -- game logic
    break  -- exit loop
end

-- Repeat-until loop
repeat
    -- code here
until condition
```

## Functions
```lua
-- Basic function
function greet(name)
    return "hello " .. name
end

-- Local function
local function calculate_damage(base, multiplier)
    return base * (multiplier or 1)  -- default parameter
end

-- Multiple return values
function get_position()
    return x, y
end
local px, py = get_position()

-- Anonymous function
local add = function(a, b)
    return a + b
end
```

## Tables (Arrays and Objects)

### Arrays (1-indexed!)
```lua
local enemies = {}
enemies[1] = "goblin"
enemies[2] = "orc"

-- Or initialize
local weapons = {"sword", "bow", "staff"}

-- Add to end
table.insert(enemies, "dragon")

-- Remove from end
local last = table.remove(enemies)

-- Remove specific index
table.remove(enemies, 2)  -- removes orc

-- Get length
local count = #enemies
```

### Objects/Dictionaries
```lua
local player = {
    x = 100,
    y = 200,
    health = 100,
    name = "hero"
}

-- Access properties
print(player.x)
print(player["health"])

-- Add new properties
player.level = 1
player["experience"] = 0

-- Check if key exists
if player.magic then
    -- has magic
end
```

## String Operations
```lua
local text = "Hello World"

-- Length
local len = string.len(text)  -- or #text

-- Substring
local sub = string.sub(text, 1, 5)  -- "Hello"

-- Find
local pos = string.find(text, "World")  -- returns 7

-- Replace
local new_text = string.gsub(text, "World", "Lua")

-- Case conversion
local upper = string.upper(text)
local lower = string.lower(text)

-- Convert numbers
local num = tonumber("42")
local str = tostring(42)
```

## Math Functions
```lua
-- Random numbers
math.randomseed(os.time())  -- seed random
local dice = math.random(1, 6)  -- 1 to 6
local chance = math.random()    -- 0.0 to 1.0

-- Common math
local abs_val = math.abs(-5)     -- 5
local max_val = math.max(10, 20) -- 20
local min_val = math.min(10, 20) -- 10
local floor_val = math.floor(3.7) -- 3
local ceil_val = math.ceil(3.2)   -- 4

-- Trigonometry (radians)
local sin_val = math.sin(math.pi / 2)  -- 1
local cos_val = math.cos(0)            -- 1
```

## Game Programming Patterns

### Game Object
```lua
function create_player(x, y)
    return {
        x = x,
        y = y,
        health = 100,
        speed = 2,
        
        update = function(self, dt)
            -- Update logic
        end,
        
        draw = function(self)
            -- Draw logic
        end,
        
        take_damage = function(self, amount)
            self.health = self.health - amount
        end
    }
end

local player = create_player(100, 100)
player:update(dt)  -- Colon syntax passes self automatically
```

### State Machine
```lua
local game_state = "menu"

function update()
    if game_state == "menu" then
        update_menu()
    elseif game_state == "playing" then
        update_game()
    elseif game_state == "paused" then
        update_pause()
    end
end

function change_state(new_state)
    game_state = new_state
end
```

### Collision Detection
```lua
function check_collision(obj1, obj2)
    return obj1.x < obj2.x + obj2.width and
           obj1.x + obj1.width > obj2.x and
           obj1.y < obj2.y + obj2.height and
           obj1.y + obj1.height > obj2.y
end
```

### Timer System
```lua
local timers = {}

function create_timer(duration, callback)
    table.insert(timers, {
        time = duration,
        callback = callback
    })
end

function update_timers(dt)
    for i = #timers, 1, -1 do
        timers[i].time = timers[i].time - dt
        if timers[i].time <= 0 then
            timers[i].callback()
            table.remove(timers, i)
        end
    end
end
```

## Common Game Variables
```lua
-- Game state
local score = 0
local lives = 3
local level = 1
local game_over = false
local paused = false

-- Timing
local dt = 0  -- delta time
local timer = 0

-- Input flags
local left_pressed = false
local right_pressed = false
local jump_pressed = false

-- Screen dimensions
local screen_width = 800
local screen_height = 600
```

## Useful Patterns

### Clamp Function
```lua
function clamp(value, min, max)
    return math.max(min, math.min(max, value))
end

-- Keep player on screen
player.x = clamp(player.x, 0, screen_width - player.width)
```

### Distance Function
```lua
function distance(x1, y1, x2, y2)
    local dx = x2 - x1
    local dy = y2 - y1
    return math.sqrt(dx * dx + dy * dy)
end
```

### Linear Interpolation
```lua
function lerp(a, b, t)
    return a + (b - a) * t
end

-- Smooth movement
player.x = lerp(player.x, target_x, 0.1)
```

### Table Utilities
```lua
-- Check if table is empty
function is_empty(t)
    return next(t) == nil
end

-- Copy table (shallow)
function copy_table(t)
    local copy = {}
    for k, v in pairs(t) do
        copy[k] = v
    end
    return copy
end

-- Find in array
function find_in_array(array, value)
    for i, v in ipairs(array) do
        if v == value then
            return i
        end
    end
    return nil
end
```

## Important Notes for Game Development

1. **Lua is 1-indexed**: Arrays start at index 1, not 0
2. **Use `local`**: Always declare variables as local for better performance
3. **Colon syntax**: `obj:method()` is shorthand for `obj.method(obj)`
4. **`ipairs` vs `pairs`**: Use `ipairs` for arrays, `pairs` for objects
5. **Performance**: Avoid creating tables in update loops
6. **Global scope**: Minimize global variables, they're slower to access

## PICO-8 Specific Notes
```lua
-- PICO-8 built-in functions you'll use:
-- _init()    -- called once at start
-- _update()  -- called 30 times per second
-- _draw()    -- called 30 times per second

-- Common PICO-8 functions:
-- print(text, x, y)    -- draw text
-- pset(x, y, color)    -- set pixel
-- pget(x, y)           -- get pixel color
-- rect(x1, y1, x2, y2, color)  -- draw rectangle
-- circfill(x, y, r, color)     -- draw filled circle
-- spr(sprite, x, y)    -- draw sprite
-- btn(button)          -- check button press
-- btnp(button)         -- check button just pressed
```

This cheatsheet covers the essential Lua concepts for game development. Focus on tables, functions, and control flow - these are the building blocks of game logic!