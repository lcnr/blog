# Implementing commutative_match

I am currently programming a game in [Rust] using the [Amethyst] game engine.
To implement collision events I simply matching on a pair of entity types.

[playground][ex0]
```rust
#[derive(Clone, Copy)]
enum EntityType {
    Environment,
    Player,
    // ..
}

fn on_collision(a: EntityType, a_data: u32, b: EntityType, b_data: u32) {
    match (a, b) {
        (EntityType::Environment, EntityType::Player) => {
            println!("environment_data: {}, player_data: {}", a_data, b_data)
        }
        (EntityType::Player, EntityType::Environment) => {
            println!("environment_data: {}, player_data: {}", b_data, a_data)
        }
        // ..
        _ => (),
    }
}

fn main() {
    on_collision(EntityType::Environment, 42, EntityType::Player, 1337);
    on_collision(EntityType::Player, 1337, EntityType::Environment, 42);
}
```

`(EntityType::Environment, EntityType::Player)` and `(EntityType::Player, EntityType::Environment)` should
cause the exact same behavior, meaning that collisions should be [commutative]. This means the current approach is quite verbose and error prone,
as one might accidentally forget to sway `a_data` and `b_data`.

As I was unable to find a solution on [`crates.io`], I decided to implement it myself. This is how I started to work on [`commutative_match`].

[Rust]:
[Amethyst]:
[commutative]:
[crates.io]
[ex0]:https://play.rust-lang.org/?version=stable&mode=debug&edition=2018&gist=a287eff18d6440790271f291ebb486e1