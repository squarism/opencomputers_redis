An alpha-quality redis client for Opencomputers.

## Installing

1. Clone this repo somewhere.  Copy `redis.lua` and `url.lua` to /usr/lib
inside your Minecraft computer.  I will look into getting this repo
registered with oppm etc.

2. Unblacklist your server if necessary.  OpenComputers.cfg in your
minecraft config folder disallows connections to localhost and common
LAN addresses.


## Example Usage

    local redis = require("redis")
    client = redis.connection('opencomputers://localhost:6379')

    -- change databases
    client:select(11)

    -- basic usage
    client:set("redis", "awesome")

    client:lpush("items", "pickaxe", "dirt")
    items = client:lrange("items", 0, 2)
    for k, v in pairs( items ) do
       print(k, v)
    end
    -- { "dirt", "pickaxe" }


    -- hash types
    client:hset("reactor", "energy", "630744")
    -- compound keys for representing something like the position of rods
    client:hset("reactor", "rod:1", "50")
    client:hset("reactor", "rod:2", "75")
    client:hset("reactor", "rod:3", "100")

    number_of_control_rods = 3
    for i=1, number_of_control_rods, 1 do
      print(client:hget("reactor", "rod:"..i ))
    end


## Gotchas

If you try to call client.set, you'll get a network error:

    redis.lua:419: attempt to index field 'network' (a nil value)

That's because this client doesn't support multi-get statements yet.  It thinks you are passing a function / block.  So don't use client.set.  Use client:set.  Only simple commands are supported right now because of a limitation of OC sockets.


## Other Cool Things

I'm currently using redis as a real-world storage place just to get data
out of minecraft.  From there, I'm pushing it to statsd.  See the sister
project
[minecraft_metrics](https://github.com/squarism/minecraft_metrics) for details on that.

