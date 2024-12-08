---
layout: post
title: Spawning More Zombies
author: Andy
summary: Spawning Waves of Zombies
---

So far we only have one zombie and if we kill that zombie then that is it nothing else happens.  What I want to happen is that when you kill all the zombies on the screen more zombies appear.

To start I created a resource called ZombieWave to keep track of the rounds of zombie wave we are on.

```
#[derive(Resource, Debug)]
struct ZombieWave(i32);
```

We can insert that in our main function and initialize the zombie wave 1, and with each new zombie wave this will be incremented by one.  The number of wave will determine how many zombies will spawn.

```
    App::new()
        .add_state::<GameState>()
        .add_plugins(DefaultPlugins)
        .add_plugins(TilemapPlugin)
        .insert_resource(ZombieWave(1))
        
```

In the zombie file a function was added called next_zombie_wave, which will be responsible for spawning more zombies.  This is going to check if there are any zombies.  If there are no zombies then spawn the number of zombies equal to the zombie wave that we are on.  So zombie wave 2 will spawn 2 zombies, wave 4 will spawn 4 zombies and so on.

The rand crate was used for creating random values, so in order for this function to work the rand crate will need to be added to the cargo.toml file.

```
pub fn next_zombie_wave(
    mut commands: Commands,
    mut player_query: Query<&Transform, With<player::Player>>,
    zombie_query: Query<Entity, With<Zombie>>,
    image_cache: Res<ImageCache>,
    mut zombie_wave: ResMut<ZombieWave>
) {

    let no_zombies = zombie_query.is_empty();
    println!("{:?}", zombie_wave.0);

    if let Ok(player_transform) = player_query.get_single_mut() {
        let player_x = player_transform.translation.x;
        let player_y = player_transform.translation.y;

        if no_zombies {
            zombie_wave.0 += 1;
            //zombie spawns but disapears.  Entity is still there just not graphic
            for n in 0..zombie_wave.0 {
                let x_pos = rand::thread_rng().gen_range(-275..=275) as f32;
                let y_pos = rand::thread_rng().gen_range(-275..=275) as f32; 
                commands.spawn((SpriteBundle {
                    texture: image_cache.zombie.clone_weak(),
                    transform: Transform::from_translation(Vec3::new(x_pos + player_x, y_pos + player_y, 1.)),
                    ..default()
                },
                Zombie));
            }

        }
    }

}
```

Add this function to the add_systems update call in the main function and more zombies will spawn once all zombies died from previous wave.

![image of src directory](/images/zombies_000.jpg)

