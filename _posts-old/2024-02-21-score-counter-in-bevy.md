---
layout: post
title: Creating a Score Counter for Bevy Game
author: Andy
summary: Adding a score counter to Bevy game.
---

The last feature that I will add before calling this project done is a score that keeps track for how many zombies you kill before you die.

I will first add a new resource called ZombieScore.

```
//Resource
#[derive(Resource, Debug)]
struct ZombieScore(i32);

// In main initialize it to 0
.insert_resource(ZombieScore(0))
```

In the main_menu function I added another TextBundle that display the score.

```
    commands.spawn((
        TextBundle {
            text: Text::from_section(
                format!("Zombies: {0}", zombie_score.0), 
                TextStyle {
                    font_size: 25.0,
                    ..default()
                },
            ),
            ..default()
        }.with_style(Style {
            position_type: PositionType::Absolute,
            top: Val::Px(5.0),
            left: Val::Px(30.0),
            width: Val:: Percent(80.),
            align_content: AlignContent::Center,
            align_items: AlignItems::Center,
            ..default()
        }), ZombieCounter));

```

I probably could have put it outside the main_menu function as it doesn't do anything for the menu screen, But for now it can just be in here.

Now that I have the text I just need to increment the number everytime we kill a zombie.

In zombies.rs where we detect the zombie bullet collision is where I will add the logic for updating the score.

```
    mut zombie_score_text: Query<&mut Text, With<ZombieCounter>>,
```

Then when we detect a collision increment the score and update the text.  Which we can do by just updating the text value.

```
                zombie_score.0 += 1;

                if let Ok(mut text) = zombie_score_text.get_single_mut() {
                    text.sections[0].value = format!("Zombies: {0}", zombie_score.0);
                }

```

Last if we did and restart the game then set the score back to 0.  I will leave the score up until the user restarts the game so that they can admire their score for however long they feel like

Which is just as easy as setting the varible to 0.

So that is it. Below is the link to the source code for the game.

(https://github.com/andyrsmith/zombie-trooper])[https://github.com/andyrsmith/zombie-trooper]