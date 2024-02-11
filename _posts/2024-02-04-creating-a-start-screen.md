---
layout: post
title: Creating a Start Screen
author: Andy
summary: Creating a start screen and adding a game state in Bevy.
---

Right now the game starts after we run it. So some sort of start screen should display when the we run the game.

The start screen will be simple.  It will display the title of the game and quick directions on how to play.

The start screen will be created in a new system called main_menu.  In the system the text will read

Zombie Trooper
AWSD to move, spacebar to shoot
Press Enter to Start

I tried to create three individual text bundles, but was unable to get the text to center on the screen like I wanted to.  What work was creating a NodeBundle and then spawning 3 children text bundles.  Doing it this way I was able to have the 3 lines of text centered on the screen.

```
   let text_style = TextStyle {
        font_size: 20.,
        ..default()
    };
    commands.spawn((NodeBundle {
        style: Style {
            width: Val::Percent(100.0),
            height: Val::Percent(100.0),
            flex_direction: FlexDirection::Column,
            justify_content: JustifyContent::Center,
            align_content: AlignContent::Center,
            align_items: AlignItems::Center,
            row_gap: Val::Px(50.),
            ..default()
        },
        ..default()
    }))
    .with_children(|parent| {
        parent.spawn((TextBundle::from_sections([
            TextSection::new("Zombie Trooper", text_style.clone()),
        ]), MainMenu));
        parent.spawn((TextBundle::from_sections([
            TextSection::new("AWSD to move, spacebar to shoot", text_style.clone()),
        ]), MainMenu));
        parent.spawn((TextBundle::from_sections([
            TextSection::new("Press Enter to Start", text_style.clone()),
        ]), MainMenu));
    });
```

Another system was create that listens for the key press of enter, and if the enter key is pressed then transitions to the next state.

```
fn check_start(keyboard_input: Res<Input<KeyCode>>, 
    query: Query<Entity, With<MainMenu>>, 
    mut commands: Commands,
    mut app_state: ResMut<NextState<GameState>>,
) {
    if keyboard_input.pressed(KeyCode::Return) {
        for text in &query {
            commands.entity(text).despawn_recursive();
        }
        app_state.set(GameState::Playing);
    }
}
```
In order to transtition to the next state we need to create the game state.  The GameState will have 3 states Start, Playing, GameOver. 

```
#[derive(States, Debug, Default, Clone, Eq, PartialEq, Hash)]
pub enum GameState {
    #[default]
    Start,
    Playing,
    GameOver,
}

```

The game state will start with the Start state, then I can add a run_if on the systems I want to run if on either start or playing.  Not using GameOver yet but I will once I add a game over screen.

```
fn main() {
    App::new()
        .add_state::<GameState>()
        .add_plugins(DefaultPlugins)
        .add_plugins(TilemapPlugin)
        .insert_resource(ZombieWave(1))
        .add_systems(Startup, load_and_cache_images)
        .add_systems(Startup, setup_game)
        .add_systems(Startup, (main_menu).run_if(in_state(GameState::Start)))
        .add_systems(Update, (check_start).run_if(in_state(GameState::Start)))
        .add_systems(Update, (player::move_player, camera::update_camera, player::player_shoot, 
            bullet::move_bullet, zombies::move_zombies, zombies::zombie_player_collision, 
            zombies::zombie_bullet_collision, bullet::despawn_bullet, zombies::next_zombie_wave)
            .run_if(in_state(GameState::Playing)))
        .run();
}

```