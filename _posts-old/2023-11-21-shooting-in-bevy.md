---
layout: post
title:  Firing Weapons in Bevy
author: Andy
summary: Getting the rectangle to fire a weapon
---

Currently I spawn a rectangle and can move that rectangle around on the screen.  Now I plan to add the ability to fire a bullet from this rectangle.

I started by creating a new function called player_shoot which will check if the player presses the space bar and if so create a circle.

```
if keyboard_input.just_released(KeyCode::Space) {
    commands.spawn((MaterialMesh2dBundle {
        mesh: meshes.add(shape::Circle::new(1.).into()).into(),
        material: material.add(ColorMaterial::from(Color::WHITE)), 
        transform: Transform::from_translation(player_transform.translation), 
        ..default()
        },
        Bullet,
        Movement {
            last_movement: player_movement.last_movement         
        }     
    ));     
}
```

To create a circle I used a MaterialMesh2dBundle instead of the SpriteBundle. SpriteBundle give you the option of representing the object as a rectangle, but not as other shapes. 
MaterialMesh2dBundle will allow us to create a cicrle.
<br/>

Added a component called bullet was becuase added so that I could just query on these object. The bullet will travel in the direction of the last movement of the player. In order to do that I created another component called movement that will hold a property called last_movement. This component will also be added to the player. We will use this property to record the last direction that the player went in and then use that property on each bullet to tell it the direction where to go using the players direction.

```
    if keyboard_input.pressed(KeyCode::A) {
        xdirection -= 1.0;
        player_movement.last_movement = Direction::LEFT;
    }     if keyboard_input.pressed(KeyCode::D) {
        xdirection += 1.0;
        player_movement.last_movement = Direction::RIGHT;
    }     if keyboard_input.pressed(KeyCode::S) {         
        ydirection -= 1.0;
        player_movement.last_movement = Direction::DOWN;
    }     if keyboard_input.pressed(KeyCode::W) {         
        ydirection += 1.0;
        player_movement.last_movement = Direction::UP;
    }
```

Using the last_movement property I can now move the bullet continously in that one direction. For the query on the player we were just able to use single_mut, but since there will be multiple bullets I will do a for loop to iterate through them all to update the x or y location for each bullet each time we update the frame.

```
fn move_bullet(mut query: Query<(&mut Transform, &mut Movement), With<Bullet>>) {
    for (mut transform, movement) in &mut query {
        if movement.last_movement == Direction::DOWN {
            transform.translation.y -= 1.0;
        } else if movement.last_movement == Direction::LEFT {
            transform.translation.x -= 1.0;
        } else if movement.last_movement == Direction::UP {
            transform.translation.y += 1.0;         
        } else {
            transform.translation.x += 1.0;        
        }
    }
}
```

These 2 method they will be added in the Update add_systems function call.  Since there are now multiple functions being called in the update will need to wrap in a ( )

```
 fn main() {     
    App::new()
        .add_plugins(DefaultPlugins)
        .add_systems(Startup, setup_game)
        .add_systems(Update, (move_player, player_shoot, move_bullet))
        .run();
}
```

Now we can move the rectangle and can fire, but it still isn't a game yet.  Next up is adding some zombies, which for now will just be some other shape.  Eventually swap out these shapes for sprites but that will be for another time.
