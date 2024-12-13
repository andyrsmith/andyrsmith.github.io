---
layout: post
title: Adding Graphics to Bevy Game
author: Andy
summary: Changing the player and zombies from shapes to sprites.
---

This time around I will update the player and zombies from shapes to actual sprites of a person and a zombie.  I will be using the graphics from Kenney.nl top down shooter pack. [https://kenney.nl/assets/top-down-shooter](https://kenney.nl/assets/top-down-shooter)

I will use the manRed_gun.png as the player which I will need to change the player entity from this

```
commands.spawn((
    SpriteBundle {
        transform: Transform { 
            translation: Vec3::new(0.0,0.0,10.0), 
            scale:PLAYER_SIZE,
            ..default()
            },
        sprite: Sprite { 
            color: PLAYER_COLOR,
            ..default()
        },


```

To this

```

    commands.spawn((
        SpriteBundle {
            texture: asset_server.load("sprites/manRed_gun.png"),
            transform: Transform::from_translation(Vec3::new(0.,0.,1.)),
            sprite: Sprite {
                flip_x: true,
                ..default()
            },
            ..default()
```

In the SpriteBundle I added a texture property which will load the asset from the assets/sprites folder and updated the transform z property to 1 because the player will be displayed on top of an image otherwise when we add the background we will not be able to see the player.

I did the same for the zombie.

We will also need the player to face a different direction depending on where we move.  Inn the movement code updated the rotation for the player depending the direction.

```
    if keyboard_input.pressed(KeyCode::A) {
        xdirection -= 1.0;
        player_movement.last_movement = Direction::LEFT;
        rotation = 0.0; 
        player_transform.rotation = Quat::from_rotation_z(rotation);
    }
```

For A(Up) rotation is 0, D(right) rotation is PI, S(down) rotation is PI / 2, and W(left) rotation is (3 * PI)/2

For the rotation of the zombie I needed to do something a little different since the zombie is just following the player.

```
let diff_x = transform.translation.x - player_transform.translation.x;
let diff_y = transform.translation.y - player_transform.translation.y;
let mut zombie_rotation = 0.0;
if diff_x.abs() > diff_y.abs() {
    if diff_x > 0.0 { 
        zombie_rotation = PI;
    } else {
        zombie_rotation = 0.0;
    }
} else { 
    if diff_y > 0.0 {
        zombie_rotation = (3. * PI) / 2.; 
    } else {
        zombie_rotation = PI / 2.;
    }
}
transform.rotation = Quat::from_rotation_z(zombie_rotation);
```

I first get the difference between the x and y position of the player and zombie, and compare the absolute value of x and y to see which one is greater in order to know should I proirize a up or down position or left or right position, once I know that then I can see if the player is above or below and rotate the zombie accordingly.

Now to add a background to the game.  For now it will just be one tile which is 64 by 64 pixels. Using bevy_ecs_tilemap and just following their basic example with the code below I was able to get a background of plan grass. The playing field is 64 by 64 tiles which is probably way too big, but I'm going to leave it like that for now.

```
let texture_handle: Handle<Image> = asset_server.load("spritesheet/spritesheet_tiles.png");
let map_size = TilemapSize { x: 64, y: 64 };
let tilemap_entity = commands.spawn_empty().id();
let mut tile_storage = TileStorage::empty(map_size);

    for x in 0..map_size.x {
        for y in 0..map_size.y {
            let tile_pos = TilePos { x, y };
            let tile_entity = commands
                .spawn(TileBundle {
                    position: tile_pos,
                    tilemap_id: TilemapId(tilemap_entity),
                    ..Default::default()
                })
                .id();
            tile_storage.set(&tile_pos, tile_entity);
        }
    }

    let tile_size = TilemapTileSize { x: 64.0, y: 64.0 };
    let grid_size = tile_size.into();
    let map_type = TilemapType::default();

    commands.entity(tilemap_entity).insert(TilemapBundle {
        grid_size,
        map_type,
        size:map_size,
        storage: tile_storage,
        texture: TilemapTexture::Single(texture_handle),
        tile_size,
        transform: get_tilemap_center_transform(&map_size, &grid_size, &map_type, 0.0),
        ..Default::default()
    })
```    

![image of gameplay](/images/manvszombie.jpg)

After putting in the background we can get the camera to follow the player first by adding a component on the comera spawn.

```
commands.spawn((Camera2dBundle::default(),CameraMaker));
```

Then when we are updating the player movement update the camera x and y to match the players.  

```
        for mut camera in &mut cameras {
            camera.translation.x = player_transform.translation.x;
            camera.translation.y = player_transform.translation.y;
        }
```

Now we can see the that the camera follows the player when we move.

Next I want to origanize the code better up to this I have just been putting it in the main.rs and as more logic gets adde
