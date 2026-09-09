#### Example marker-type state machine
```rust

use std::marker::PhantomData;

pub struct Grounded;
pub struct Launched;

pub struct Rocket<Stage = Grounded> {
    fuel_kg: f64,
    stage: PhantomData<Stage>,
    velocity: f64,
}

impl Rocket<Grounded> {
    pub fn new(fuel_kg: f64) -> Self {
        Self { fuel_kg, stage: PhantomData, velocity: 0.0 }
    }

    // Takes `self` by value: a grounded rocket cannot be launched twice
    // and `accelerate` does not exist until the rocket has been launched
    pub fn launch(self) -> Rocket<Launched> {
        Rocket { fuel_kg: self.fuel_kg, stage: PhantomData, velocity: self.velocity }
    }
}

impl Rocket<Launched> {
    pub fn accelerate(&mut self) {
        self.fuel_kg -= 1.0;
        self.velocity += 100.0;
    }
}

// Rockets in either stage share some state that can be read
impl<Stage> Rocket<Stage> {
    pub fn fuel_kg(&self) -> f64 {
        self.fuel_kg
    }

    pub fn velocity(&self) -> f64 {
        self.velocity
    }
}
```