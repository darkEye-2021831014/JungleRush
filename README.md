<div align="center">

<img src="assets/Icons/JungleRush.png" alt="Jungle Rush logo" width="160"/>

# Jungle Rush

**A 2048-inspired endless road runner built with Java and libGDX.**
Drive through the jungle, absorb weaker cars, dodge the strong ones, and merge your way to an astronomically large score.

![Java](https://img.shields.io/badge/Java-8%2B-orange?logo=openjdk&logoColor=white)
![libGDX](https://img.shields.io/badge/libGDX-1.12.1-red)
![Gradle](https://img.shields.io/badge/Gradle-8.5-02303A?logo=gradle&logoColor=white)
![Platform](https://img.shields.io/badge/Platform-Windows%20%7C%20macOS%20%7C%20Linux-blue)
![License](https://img.shields.io/badge/License-MIT-green)

</div>

---

## Table of Contents

- [About the Game](#about-the-game)
- [How to Play](#how-to-play)
- [Controls](#controls)
- [Features](#features)
- [Getting Started](#getting-started)
- [Building a Distributable](#building-a-distributable)
- [Leaderboard](#leaderboard)
- [Project Structure](#project-structure)
- [Tech Stack](#tech-stack)
- [Contributing](#contributing)
- [License](#license)

---

## About the Game

Jungle Rush takes the merge mechanic of **2048** and puts it on a jungle highway. Every vehicle and animal on the road carries a power-of-two value, and so do you. Run into something weaker or equal and you absorb it. Run into something stronger and the run is over.

The road is flanked by a river on both sides, so careless steering ends the run just as fast as a bad collision.

## How to Play

Your car starts with a small value. Everything on the road is labelled with its own number.

| You hit | What happens |
| --- | --- |
| **An enemy car with a value ≤ yours** | You absorb it. Its value is added to your Score Tree. |
| **An enemy car with a value > yours** | **Game over.** |
| **An animal (additive)** | Its value is added to your Score Tree. |
| **An animal marked `/` (divide)** | Your score is divided by its value. If you are left with nothing, the run ends. |
| **The river (road border)** | **Game over.** |

A few more rules worth knowing:

- **Score Tree.** Your values live in a tree shown on the right side of the screen. Whenever two equal values meet, they merge into one doubled value, exactly like a 2048 tile. Your displayed score is the largest value in the tree.
- **Animals affect traffic too.** If an animal runs into an enemy car, it either boosts that car's value or divides it. Watch the road, because a weak car can quietly become a dangerous one.
- **Huge numbers are abbreviated.** Scores use `BigInteger` internally, so they never overflow. Large values are shown with suffixes such as `K`, `M`, `B`, and `T`, continuing far beyond.
- **Your final result is your highest score reached** during the run, not the value you ended with.

## Controls

| Key | Action |
| --- | --- |
| `←` / `A` | Steer left |
| `→` / `D` | Steer right |
| `↑` / `W` | Speed up (faster steering, and enemy cars close in faster) |
| `↓` / `S` | Slow down (slower steering, enemy cars halt, animals rush in faster) |
| `Space` | Pause / resume |
| `L` | Toggle the line indicator |

The main menu starts with **Space** (or a click). The pause screen also offers Resume, Scorecard, Music, Sound, and Quit buttons.

## Features

- Merge-based scoring system with arbitrarily large numbers
- Cars and animals as interactive obstacles and power-ups
- 120+ jungle tree sprites lining the road
- Pause menu with music and sound toggles, plus an in-game scorecard
- Five background music tracks and a full set of sound effects
- Online leaderboard with automatic offline fallback
- Player name entry with validation (letters and spaces, up to 15 characters)
- Windows installer built automatically with GitHub Actions

## Getting Started

### Prerequisites

- **JDK 8 or newer.** The project is compiled for Java 8 compatibility, and the CI pipeline builds with JDK 21.
- **Git**, to clone the repository.
- No separate Gradle install is needed. The included Gradle wrapper (8.5) handles it.

### Clone and Run

```bash
git clone https://github.com/darkEye-2021831014/JungleRush.git
cd JungleRush
```

**Linux / macOS**

```bash
./gradlew desktop:run
```

**Windows**

```bat
gradlew.bat desktop:run
```

> On macOS, the Gradle `run` task automatically adds the `-XstartOnFirstThread` JVM flag that LWJGL3 requires. If you launch the game from your own run configuration, add that flag yourself.

The game opens in a 1280×800 window at 60 FPS with VSync enabled.

### Running from an IDE

1. Import the project as a **Gradle** project (IntelliJ IDEA or Eclipse).
2. Run `com.junglerush.DesktopLauncher` from the `desktop` module.
3. Set the **working directory to the `assets` folder**, because the game loads files by relative path.

## Building a Distributable

### Fat JAR

```bash
./gradlew desktop:dist
```

The runnable JAR is written to `desktop/build/libs/`:

```bash
java -jar desktop/build/libs/desktop-1.0.jar
```

### Windows Installer

The repository includes a GitHub Actions workflow (`.github/workflows/windows-build.yml`) that, on every push to `main` or on manual dispatch, will:

1. Set up Temurin JDK 21
2. Build the fat JAR with `./gradlew desktop:dist`
3. Package it into a `.exe` installer with `jpackage`, using the project icon
4. Upload the installer as the **JUNGLE-RUSH-Windows** workflow artifact

To get the installer, open the **Actions** tab, pick the latest successful run, and download the artifact.

## Leaderboard

Jungle Rush keeps a **top 14** leaderboard, shown on the game-over screen and the in-game scorecard.

- **Online:** when an internet connection is detected, scores are loaded from and saved to a shared MongoDB database.
- **Offline:** without a connection, scores are read from and written to a local file, `Files/highestScores.txt`, inside your user home directory (libGDX external storage).
- Scores are stored as `name:score` lines and sorted from highest to lowest using `BigInteger` comparison.

## Project Structure

```
JungleRush/
├── assets/                  # Textures, fonts, music, sound effects, icons
│   ├── Background/          # Menu and road art, player car, logo
│   ├── Enemy/               # Living (animals) and NonLiving (cars) sprites
│   ├── Trees/               # Forest tree sprites
│   ├── Menu/                # Pause menu buttons
│   ├── Music/               # Background tracks
│   ├── Sounds/              # Sound effects
│   ├── Fonts/               # Roboto Mono
│   ├── Icons/               # App icons (.ico, .icns)
│   └── Files/               # Default leaderboard file
├── core/                    # Platform-independent game logic
│   └── src/com/junglerush/
│       ├── JungleRush.java          # Game entry point, screen management
│       ├── MainMenuScreen.java      # Title screen and name prompt
│       ├── GameScreen.java          # Main gameplay loop and spawning
│       ├── GameOverScreen.java      # Results and leaderboard
│       ├── PauseScreen.java         # Pause menu
│       ├── Player.java              # Player car and Score Tree merging
│       ├── Enemy.java               # Enemy cars and animals
│       ├── Collision.java           # Collision rules
│       ├── ScoreManager.java        # Leaderboard (online + local)
│       ├── MusicManager.java        # Background music control
│       ├── Background.java          # Scrolling road and river
│       ├── Trees.java               # Forest edge rendering
│       ├── Indicator.java           # Visual indicators for incoming enemies
│       └── NumberFormatter.java     # K / M / B / T style abbreviations
├── desktop/                 # Desktop launcher (LWJGL3)
│   └── src/com/junglerush/DesktopLauncher.java
├── .github/workflows/       # CI: Windows installer build
├── build.gradle
├── settings.gradle
└── gradlew / gradlew.bat
```

## Tech Stack

- **Language:** Java
- **Framework:** [libGDX](https://libgdx.com/) 1.12.1 with the LWJGL3 backend
- **Libraries:** gdx-freetype (fonts), gdx-box2d, box2dlights, Ashley, gdx-ai
- **Database:** MongoDB (Java sync driver 5.1.2) for the online leaderboard
- **Build:** Gradle 8.5 with the Shadow plugin
- **CI/CD:** GitHub Actions and `jpackage`

## Contributing

Contributions, bug reports, and ideas are welcome.

1. Fork the repository
2. Create a feature branch: `git checkout -b feature/my-feature`
3. Commit your changes: `git commit -m "Add my feature"`
4. Push to the branch: `git push origin feature/my-feature`
5. Open a pull request

## License

This project is licensed under the **MIT License**. See the [LICENSE](LICENSE) file for details.

## Author

**Ashraful Islam** ([@darkEye-2021831014](https://github.com/darkEye-2021831014))

---

<div align="center">

If you enjoyed the game, consider giving the repo a ⭐

</div>
