# embassy_experiments

My embassy experiments

## setup

### Rust

Install Rust, preferably nightly. See https://www.rust-lang.org/tools/install . Install rustup https://rustup.rs/ . Remember ```rustup update``` regularly.

### Editor

- helix: https://github.com/helix-editor/helix
- rust-analyzer (as currently recommended by helix): https://github.com/rust-lang/rust-analyzer ; for now: ```rustup component add rust-analyzer``` .
- check health: ```helix --health```

### Embassy

- follow installation instructions at https://embassy.dev/book/dev/getting_started.html .
- install probe-run, both the necessary dependencies and the package itself: ```cargo install probe-run --features cli``` . Note that you may need to install the newest probe-rs in the newest version if the chip you use is very recent and not yet included in the probe-rs current release: ```https://github.com/probe-rs/probe-rs```.
- follow the installation instruction.

### Check that everything works

I have a stm32u5 board - but you may want to use another board depending on what you have. More specifically, my board is a nucleo board with a stm32u5a5zj chip, so I do a couple of edits to the config:

```
kzm@kzm-bpq:~/Desktop/Git/embassy/examples/stm32u5$ git diff Cargo.toml
-# Change stm32u585ai to your chip name, if necessary.
-embassy-stm32 = { version = "0.1.0", path = "../../embassy-stm32", features = ["nightly", "defmt", "unstable-pac", "stm32u585ai", "time-driver-any", "memory-x" ]  }
+# Change stm32u5a5zj to your chip name, if necessary.
+embassy-stm32 = { version = "0.1.0", path = "../../embassy-stm32", features = ["nightly", "defmt", "unstable-pac", "stm32u5a5zj", "time-driver-any", "memory-x" ]  }
```

I can then for example choose the blinky binary and check that it compiles:

```
kzm@kzm-bpq:~/Desktop/Git/embassy/examples/stm32u5/src/bin$ cargo build --bin blinky --release
    Finished release [optimized + debuginfo] target(s) in 0.14s
```

I can also connect a cable between the STLK (ST-Link) USB port and my machine, and check that I can upload the script on my board: (to check which chips are supported, use ```probe-rs chip list```):

- I can see that the ST-Link is connected:

```
kzm@kzm-bpq:~$ probe-rs list
The following debug probes were found:
[0]: STLink V3 (VID: 0483, PID: 374e, Serial: 001B00423232510139353236, StLink)
```

- I can set the correct chip output: (note: seems like stm32u5a5 is not supported yet by probe-rs?)

```
kzm@kzm-bpq:~/Desktop/Git/embassy/examples/stm32u5/src/bin$ cargo run --bin blinky --release
    Finished release [optimized + debuginfo] target(s) in 0.14s
     Running `probe-rs run --chip STM32U585AIIx /home/kzm/Desktop/Git/embassy/examples/stm32u5/target/thum
bv8m.main-none-eabihf/release/blinky`
     Erasing sectors ✔ [00:00:00] [#########################] 24.00 KiB/24.00 KiB @ 301.78 KiB/s (eta 0s )
 Programming pages   ✔ [00:00:00] [##########################] 20.00 KiB/20.00 KiB @ 36.86 KiB/s (eta 0s )
    Finished in 0.641s
0.000000 DEBUG rcc: Clocks { sys: Hertz(4000000), apb1: Hertz(4000000), apb1_tim: Hertz(4000000), apb2: He
rtz(4000000), apb2_tim: Hertz(4000000), apb3: Hertz(4000000), ahb1: Hertz(4000000), ahb2: Hertz(4000000), 
ahb3: Hertz(4000000) }
└─ /home/kzm/Desktop/Git/embassy/embassy-stm32/src/fmt.rs:130
0.000061 INFO Hello World!
└─ blinky.rs:14   
0.000305 INFO on! 
└─ blinky.rs:19   
0.200714 INFO off!
└─ blinky.rs:23    
```

### Starting a new embassy based project

We assume that the current embassy_experiments repository is cloned in a Git folder alongside the embassy repository.

See the examples/blink for a minimalistic example; you can for example copy-paste the 
