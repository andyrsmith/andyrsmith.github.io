---
layout: post
title:  Creating Enemies
author: Andy
summary: Adding enemies and setting up collision detection
---

Next up we should add some enemies.  In my setup_game function I added a new spawn to create this one zombie.

```

    commands.spawn((MaterialMesh2dBundle {
        mesh: meshes.add(shape::RegularPolygon::new(ZOMBIE_RADIUS, 6).into()).into(),
        material: materials.add(ColorMaterial::from(Color::GREEN)),
        transform: Transform::from_translation(Vec3::new(150., 0., 0.)),
        ..default()
    },
    Zombie));

```

This time instead of a rectangle I added a hexagon for the zombie. One argument is the number of sides for the shape which will be 6, and the other argument is the radius of the shape, which I have set as 15 for now.  I added a component of zombie so I can query for the specific object on that component later.

Now that the zombie or hexagon appears in the game it should probably move on it's own.  I have created this new function of move_zombies that will move towards the player.

```
fn move_zombies(mut player_query: Query<&mut Transform, (With<Player>, Without<Zombie>)>, 
    mut zombie_query: Query<&mut Transform, (With<Zombie>, Without<Player>)>) {
    if let Ok(player_transform) = player_query.get_single_mut() {
        for mut transform in &mut zombie_query {
            const ZOMBIE_SPEED: f32 = 0.5;
            if player_transform.translation.x > transform.translation.x {
                transform.translation.x += ZOMBIE_SPEED;
            } else {
                transform.translation.x -= ZOMBIE_SPEED;
            }
    
            if player_transform.translation.y > transform.translation.y {
                transform.translation.y += ZOMBIE_SPEED;
            } else {
                transform.translation.y -= ZOMBIE_SPEED;
            }
        }
          
    }

}

```

There are 2 queries for this function one for the player and another for the zombies. There will only be one player one the screen at a time so we can do a get_single_mut() to retreive the player instance. At some point there will be more zombies on the screen, so I will iterate through each zombies. The zombies will move at a slower speed then the player. If they are the same speed then it will be hard for the player to move away from them.

Next up to add some collision detection.  One for the player hitting a zombie and the other for the bullet hitting the zombie.

```
fn zombie_player_collision(
    mut commands: Commands,
    mut player_query: Query<(Entity, &Transform), With<Player>>,
    zombie_query: Query<&Transform, With<Zombie>>
) {
    if let Ok((player_entity, player_transform)) = player_query.get_single_mut() {
        for transform in &zombie_query {
            let distance = player_transform.translation.distance(transform.translation);
            let player_radius = PLAYER_SIZE.x / 2.0;
            if distance < player_radius + ZOMBIE_RADIUS {
                println!("Game Over");
                commands.entity(player_entity).despawn();
            }
        }
    }
}
```

We can get the distance between the player and zombie with calling the distance function that is on the transform.translation property.  That will give distance between the center point of the player and zombie, but we want collision to happen when the outer edge of the player and zombie touch.  I will just need to divid the player size by 2 to get the player radius and I already have the zombie radius.  Now we can just add the player and zombie radius togather to see if the touch.  If they do print Game Over and despawn the player.

For the bullet and zombie collision we will do the same thing just we will despawn both the zombie and bullet.

```
fn zombie_bullet_collision(
    mut commands: Commands,
    bullet_query: Query<(Entity, &Transform), With<Bullet>>,
    zombie_query: Query<(Entity, &Transform), With<Zombie>>
) {
    for (bullet_entity, bullet_transform) in &bullet_query {
        for (zombie_entity, zombie_transform) in &zombie_query {
            let distance = bullet_transform.translation.distance(zombie_transform.translation);
            if distance < ZOMBIE_RADIUS + BULLET_RADIUS {
                println!("Zombie Shot!");
                commands.entity(bullet_entity).despawn();
                commands.entity(zombie_entity).despawn();
            }

        }
    }

}
```

These new functions were added to the Update system in the main function.  

Now there is a zombie, it moves, and the zombie and player can died.  It is getting closer to being a game.