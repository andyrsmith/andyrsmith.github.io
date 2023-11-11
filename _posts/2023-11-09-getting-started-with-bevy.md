---
layout: post
title: Getting Started with Bevy 
author: Andy
summary: Setting up a new Bevy project
---

## What I am building 

In this project I am going to try to develop a simple game in using the Bevy engine.  Bevy is still early in development, but it seems like it has the features we need to work on this game.
I plan on making a top down 2d shooter game in which you must eliminate all of the enemies in the level to advance. Eventually we will use some game sprites from the [Kenney](http://www.kenney.nl) asset to make the game look a little nicer, but to start we will use just basic shapes.

## Getting Started with Bevy 

Before we begin setting up Bevy we must make sure that our version of Rust is on the latest stable version.  It is noted that the mininum support version for Rust in Bevy is the latest stable version. 


Once we verify that we can add Bevy as a dependency in my cargo toml. I am going to be using Bevy version 0.11.

``` 
[dependencies] bevy = "0.11"

``` 

In the Bevy docs it says that we need to put the following in our Cargo.toml file because WGPU requires us to put this resolver in our toml file.
```
[workspace] resolver = "2" 

```

If we go to the WGPU docs it says that you only need to add that if you are using 2018 TOML version.  I don't think we need this because 2021 version rust uses resolver 2 by default, but I will add leave it in for now.  This resolver will ignore features enabled on platform specific dependencies that we are not currently building on. 

For larger games it is recommended that we add in a couple of configurations in order to build faster.  This may not be needed for this project but I will go ahead and add it in.

``` 
# Enable a small amount of optimization in debug mode 
[profile.dev] opt-level = 1 
# Enable high optimization for dependencies (incl Bevy), but not our code 
[profile.dev.package."*"] opt-level = 3 

``` 

The opt-level represents the level of optimization rust will do at compile time.  The number goes from 0 to 3, with 0 being no optimization to 3 being all optimization.  Bevy suggest that we set the dev to basic optimization and high optimization for any dependencies that we use, so anything that isn't our code.  

There are a couple of other options we can add to increase compile time, but not sure if we will need them with this game so we will leave them out for now.  We can always come back to them.

## Get Blank project working 

To get the basic bevy project working we will go to the main.rs file. At the top add the bevy library 

``` 
use bevy::prelude::*; 
``` 
In the main function add in  
``` 
App::new() 
  .add_plugins(DefaultPlugins).run(); 
``` 

Bevy engine features are implemented as plugins and the DefaultPlgins gives us access to th UIPlugin, WindowPlugin, InputPlugin, and more.  

The complete list is here [https://docs.rs/bevy/latest/bevy/struct.DefaultPlugins.html](https://docs.rs/bevy/latest/bevy/struct.DefaultPlugins.html). We don't have to use the default plugin, we are free to pick which plugins that we want to use or write our own, but this does give us what we need to get started.  

If you cargo run this then it will open up a blank window.  I know not very exciting but in the next post we will add different shapes in the window. 
