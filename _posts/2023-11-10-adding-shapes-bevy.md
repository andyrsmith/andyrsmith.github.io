---
layout: post
title: Create Shapes using Bevy
author: Andy
summary: Lets add a rectangle to our project
---

Where I last left off we just had a blank window, so now I'll add something to that.


## Adding a System

In our main function I added a new system. Systems are a piece functionallity that Bevy will run. 

```
App::new()
  .add_plugins(DefaultPlugins)
  .add_systems(Startup, setup_game)
  .run();
```

There are two agruments passed into this system.  One is the keyword Startup meaning it will run once the program starts, and the other is a function that I will define called setup_game.

In this function I will start by adding a Camera to the game.

```
fn setup_game(mut commands: Commands) {
    commands.spawn(Camera2dBundle::default());
}
```

The camera is responible for rendering content in the window.  Once the first object if you try removing the code for the camera and you will see that nothing will render on the screen.

## Adding the Rectangle

So next let's get that rectangle added.
```
commands.spawn(SpriteBundle {
    transform: Transform {
        translation: Vec3::new(0.0, 0.0, 0.0),
        scale: Vec3::new(50.0, 30.0, 0.0),
        ..default()
    },
    ..default();
}
```

Now if you compile this you should see a rectangle on the screen.  The translation property is the place in the window that the object will appear.  First value is x, second is y, and the last is the z-ordering.  The zero values mean that is it in the middle of the window.  Scale will be the size of the rectangle and the value for the arguments are also x, y, and z.  If you adjust the x and y values for each of the properties go can adjust where the rectangle will appear on the screen and how big you it should be. 

We can add a color to the rectangle as well.

```
commands.spawn(SpriteBundle {
    transform: Transform {
        translation: Vec3::new(0.0,0.0,10.0), 
        scale:PLAYER_SIZE,
        ..default()
    },
    sprite: Sprite {
        color: Color::rgb(0.3, 0.3, 0.7);
        ..default()
    },
    ..default();
}
```
We pass in a sprite property that includes the color that we want.  We can use the rgb coloring scheme or there are some const that we can use for the common colors. 

Common colors are listed here [https://docs.rs/bevy/latest/bevy/render/color/enum.Color.html](https://docs.rs/bevy/latest/bevy/render/color/enum.Color.html).

Next up is getting the rectangle to move on key commands.




