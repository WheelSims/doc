# Overview

The WheelSims project is a set of blocks distributed on different repositories, that can be shared among wheelchair simulators to accelerate the development of virtual reality in wheelchair training.

## Hardware overview

The WheelSims system combines:

- **Haptics** - so that the user feels realistic forces at the wheels
- **Virtual Reality** - so that the user feels where they navigate
- **Incline Platform** - so that the user feels the difference between ascending, descending or crossing a slope.

These three systems are independent. For example, it is not required to have an haptic system to navigate in virtual reality. They are complementary, they add realism to the simulation.

This figure illustrates the data flow between the different systems:


```mermaid
flowchart TD

force_sensor["<b>Force Sensor</b><br/>(AMTI force cell)"]
haptics["<b>Haptics System</b><br/>(Simulink Real-Time)"]
unity["<b>Virtual Reality System</b><br/>(Unity3D)"]
motors["<b>Motors</b><br/>(attached to rollers)"]
rollers["<b>Rollers</b>"]
projectors["<b>Projectors</b>"]
dbox["<b>Incline platform</b><br/>(D-Box System)"]

force_sensor -->|Force| haptics
haptics -->|Torque command| motors
motors --> rollers
rollers -->|Speed| haptics
rollers -->|Speed| unity
unity -->|Visual| projectors
unity -->|Incline| dbox
unity -->|Rolling Resistance| haptics

```


## Software overview

The project uses a git-based versioning system, with heavy use of git submodules. All toplevel repositories start with `sim_`:

- `sim_generic`: A generic repository not tied to specific hardware. This "simulator" includes every available submodule and is mainly for testing and developing the submodules.
- `sim_irglm`: The IRGLM high-realism simulator.
- `sim_racing`: A wheelchair racing simulator with user-power biofeedback.

New simulators normally use `sim_generic` as a template.

The folder/submodule hierarchy in the `sim_generic` repository is presented below. Note that any other simulator should follow a similar hierarchy.

```mermaid
flowchart LR

sim_generic["sim_generic<br/>(main repository)"]

unity("Unity<br/>(folder)")
assets("Assets<br/>(folder)")
unity2("Library<br/>(folder)")
unity3("Packages<br/>(folder)")
unity4("etc.")

vr_assets_common["vr_assets_common<br/>(git submodule)"]
vr_art_source["vr_art_source<br/>(git submodule)"]

scenes("Scenes<br/>(folder)")
user("User<br/>(folder)")
assets_etc("etc.")

haptics("Haptics<br/>(folder)")

haptics_common["haptics_common<br/>(git submodule)"]


sim_generic --> unity
unity --> assets
unity --> vr_art_source
unity --> unity2
unity --> unity3
unity --> unity4

assets --> vr_assets_common
assets --> scenes
assets --> user
assets --> assets_etc

sim_generic --> haptics

haptics --> haptics_common


```
with:

- `vr_assets_common` submodule: An asset submodule that includes all shared objects and scripts, from the smallest object to whole environments, available as drag-drop prefabs.
- `Scenes` folder: The folder that includes the specific scenes for a given simulator. A scene normally consists in at least an environment from the `vr_assets_common submodule`, and a user from the `User` folder.
- `User` folder: A folder that includes a set of colliders, cameras and scripts that represents the user in its environment. This also includes scripts to communicate with the haptics system (if present), the incline system (if present), and other instruments.
- `vr_art_source` submodule: A repository of construction material for the assets and scenes, such as Blender files. This repository does not contain the textures: textures are hosted as assets on their corresponding repositories.
- `Haptics` folder: A folder that contains the Simulink Real-Time material for the haptics system. Apart from the git submodule, the contents of this folder is specific to this simulator.
- `haptics_common` submodule: A submodule of Simulink blocks that are shared between simulators, such as the dynamical model of a wheelchair, motor controllers, etc.
