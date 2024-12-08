---
layout: post
title: Keyboard movement in Bevy
author: Andy
summary: Adding movement to our player
---

In the last post I added a rectangle, so now with a little more code we will be able to move this rectangle around the window.

## Adding a New System

Last time we added a system that runs on startup. This time below it we add a new system with agrments Update, which will run about 60 frames per second.


```
App::new()
    .add_plugins(DefaultPlugins)
    .add_systems(Startup, setup_game)
    .add_systems(Update, move_player)
    .run();
```

In this function will move the rectangle around on the screen depending on which keys we pressed.

In this function I will need to pass in two arguments. One for the the keyboard resource, and another for the query that retreive the player entity.

```
fn move_player(keyboard_input: Res<Input<KeyCode>>, mut query: Query<&mut Transform, With<Player>>,)
{     
    let mut player = query.single_mut();
    let mut x_direction = 0.0;
    let mut y_direction = 0.0;
}
```
Since I know there is only one entity with the player component I can use single_mut to access it.  If there were multiple I would have to iterate over the query, and then determine which one to update.  If we try to use single_mut on a multiple query we will get an error.

Next I'm going to check for the keys are pressed, and set the x and y accordingly.

```
    if keyboard_input.pressed(KeyCode::A) {
        x_direction -= 1.0;
    }     
    if keyboard_input.pressed(KeyCode::D) {
        x_direction += 1.0;
    }     
    if keyboard_input.pressed(KeyCode::S) {
        y_direction -= 1.0;
    }
    if keyboard_input.pressed(KeyCode::W) {
        y_direction += 1.0;
    }
```

I'm going to use the WASD keys to control movement, but we could have also used the arrow keys if we wanted. 

Lastly we will add the new x and y direction to the players current position. 

```
    player.translation.y = player.translation.y + y_direction;
    player.translation.x = player.translation.x + x_direction;
```

After that code we now have a rectangle that we can move around the screen.  Next we will try to get our player to shoot.


```
fn move_player(keyboard_input: Res<Input<KeyCode>>, mut query: Query<&mut Transform, With<Player>>,)
{     
    let mut player = query.single_mut();
    let mut x_direction = 0.0;
    let mut y_direction = 0.0;

    if keyboard_input.pressed(KeyCode::A) {
        x_direction -= 1.0;
    }     
    if keyboard_input.pressed(KeyCode::D) {
        x_direction += 1.0;
    }     
    if keyboard_input.pressed(KeyCode::S) {
        y_direction -= 1.0;
    }
    if keyboard_input.pressed(KeyCode::W) {
        y_direction += 1.0;
    }

    player.translation.y = player.translation.y + y_direction;
    player.translation.x = player.translation.x + x_direction;

}
```
