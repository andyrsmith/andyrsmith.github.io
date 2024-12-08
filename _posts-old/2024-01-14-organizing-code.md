---
layout: post
title: Organizing Code Better
author: Andy
summary: Moving code out of main.rs to seperate files
---

Up to this point all of the game logic has been going into main.rs.  What I want to do now is move code into similar chunks.

First I created a player.rs file.  This will contain all of my logic for the player entity.

The move_player and player_shoot function will be moved into here.  The distance component will be in here too, since it is used when the player shoots. It could be in its own file too, but since it is just this small component it can live here.

To use this file in the main.rs we just add the following

```
mod player

```

Whatever in that file that has the pub keyword we will be able to use in the main.rs file by the following.

```
player::player_shoot()

```

To use this in other files other then main.rs we add

```
use crate::player
```

I continued to create the following zombies.rs, movement.rs, bullet.rs, and camera.rs.  Each file stores the logic to match the name.  This will just make it easier to find the logic rather than trying to look in one big file.

![image of src directory](/images/directory.jpg)

