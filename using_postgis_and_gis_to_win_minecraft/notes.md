# Using PostGIS and QGIS to Win Minecraft

## Outline

- 2 min: Intro
    - What is Minecraft?
    - The Minecraft coordinate system.
    - What's the goal of the game?
- 2 min: The exploit
    - CPacketPlayerDigging
    - Server responds with an SPacketBlockChange
- 4 min: What to do with the data?
    - Bringing it into QGIS

## (01. title; intro)

- A huge hobby of mine is finding exploits in video games and messing around
- I've never really done anything with GIS tools before
- I suddenly found myself with all this geographic data I needed to analyze
- Seemed something like QGIS would be the right tool for the job
- It might be interesting for folks to see the approach I took, having 0 knowledge on how any of this stuff works

## (02. what is minecraft; 3d cartesian coordinate system)

- sandbox video game
- you can build with a variety of different blocks in a 3D procedurally generated world

- it uses a 3d cartestian coordinate system
- the x-axis indicates the player's distance east/west
- The z-axis indicates the player's distance north/south
- the y-axis indicates how high or low

## (03. what is minecraft; looking at a block)

- so each block in the game is assigned a specific coordinate

## (04. what is minecraft; looking at a zoomed)


## (05. what is minecraft; zero zero; how do i play?)

- when you connect to a server for the first time, your player starts at 0,0
- since everyone starts at 0,0, the place is a mess
- the gist is to get as far away from 0,0 as possible so you can build without anyone finding you
- people take it super seriously, and will walk upwards of 24 hours to build millions away from 0,0
- so i'm always trying to find clever ways to find these secret bases

## (06. the exploit; cpacketplayerdigging)

- that leads me to this packet
- the client sends this when it wants to break a block


## (07. the exploit; spacketblockchange)

- the server sends this to say like "hey some metadata on this block over here was updated"

## (08. the exploit; tying it together)

- as far as i know, 0 day exploit for minecraft
- pretend we're at 0,0. we want to know if someone is at 5000,5000.
- send a digging packet that says hey, i broke a block at 5000,5000.
- if someone is online near 5000,5000, the server responds with a block change packet denying our action.

- after getting around rate limiting, 20,000 scans a second.


^ This is an actual snippet of the exploit's implementation. So after I got around serverside rate limiting, I was able to run about 20,000 scans a second, and populate a PostGIS database of coordinates where players were actively online.

## (09. what to do with the data; screenshot of postgis)

- and i populated a PostGIS database with about 50,000 hits.
- i've collected this data over a huge period of time. some of it was backfilled from an SQLite DB from v1 of the exploit.
- so how do we get something meaningful out of this? where are people's bases and builds?

## (10. what to do with the data; spatial reference system)

- i hit a wall with the concept of a spatial reference system, i had no idea what this meant.
- i ended up storing things as a Geometry type in PostGIS to get around this, and QGIS provided acceptable results when i just selected the default.

- muncipatlity of denmark into minecraft using EPSG:25832, special spatial reference system for Europe.
- they could use something called a projection to translate from Minecraft coords to IRL coords. weird.

## (11. what to do with the data; screenshot of all the data in qgis)

- so once it was qgis, i immediately saw patterns and was able to understand the data.
- nether highways along the axes, these are predug highways people use to get around fast. no bases near here. too dangerous.

## (12. what to do with the data; nether highway)


## (13. what to do with the data; filtering out junk)

- no highways
- no beginner bases


## (14. what to do with the data; after filtering)

- we get a representation of points that are much more likely to be bases

## (15. what to do with the data; point cluster)

- if we get a lot of hits within a certain area over a period of time, that must be a base.
- if it's a one off, someone is just travelling.
- i tried heatmaps too, but it wasn't as telling.


## (16. what to do with the data; point cluster 2)

- so this guy with like 79 hits, it's probably a base


## (17. what to do with the data; did we find someone)

- he's not travelling, the points are a mesh, and sort of rectangular, so that looks like we could be capturing them all over their compound.

## (18. what to do with the data; is there a pattern)

- we can see if there's a time and date pattern

## (19. what to do with the data; yup, there is a pattern)

- someone has been logging in consistantly and playing, so it's pretty likely that this is a base
- i don't get much time to play
- i never actually found out for real
- i wanted to use this as an example because the overworld has a larger dataset

## (20. what to do with the data; space valk qgis)

- there's another dimension called the end. hardcore players, because it's harder to travel there.
- finding this guy's base was pretty much the whole reason for collecting all this data.
- there are political motivations behind it that have been bent up after 10 years or so.

## (21. what to do with the data; wide shot of space valk)


## (22. what to do with the data; group selfie)


## (23. moving forward)