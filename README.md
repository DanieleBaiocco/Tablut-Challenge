# Tablut Challenge — INiegghie

`INiegghie` is a Java player for **Ashton Tablut**, developed for the Tablut competition framework used at the University of Bologna.

Tablut is an asymmetric strategy game played on a 9×9 board. The white side protects a king and tries to escort it to one of the escape squares on the edge of the board, while the black side attempts to surround and capture it. This repository contains a client capable of playing either side by connecting to a compatible Tablut game server.

## How the player works

The agent chooses its moves through a depth-limited **Minimax search with alpha-beta pruning**. White is treated as the maximizing player and black as the minimizing player, while terminal and intermediate positions are evaluated from White's perspective.

The default search depth is `4`, although a different value can be supplied when launching the client. Move computation runs under the timeout received from the server, leaving a small margin before the move deadline expires.

The evaluation function combines several features of the current position. In particular, it considers whether either side has already won, the king's available escape paths, the number of obstacles between the king and the edge, the remaining white and black pieces, the danger around the king, pieces that may be captured on the following move, and escape squares controlled or blocked by black pieces.

Legal moves and successor states are generated locally according to the Ashton Tablut board, including the throne, camps, captures and the different movement rules for attackers, defenders and the king.

The repository also contains an experimental genetic-algorithm package designed to represent and evolve sets of heuristic weights. The executable player currently uses the static weights defined in `HeuristicNiegghie`.

## Requirements

To compile and run the project from source, install:

- a Java Development Kit supporting Java 9 or later;
- Apache Ant;
- a compatible Tablut competition server.

The required Java libraries are already included in `Tablut/lib`.

## Running a match

The server is not bundled with this repository. A compatible implementation is available in the original competition project:

```bash
git clone https://github.com/AGalassi/TablutCompetition.git
cd TablutCompetition/Tablut
ant clean
ant compile
ant server
```

Once the server is running, open a separate terminal for each player.

The repository includes a prebuilt executable JAR in:

```text
Tablut/Executables/tablut/TablutINiegghie.jar
```

On Linux, macOS or Git Bash, enter that directory and make the launcher executable:

```bash
cd Tablut/Executables/tablut
chmod +x runmyplayer.sh
```

Start the agent as White:

```bash
./runmyplayer.sh white 60 localhost
```

Start another instance as Black:

```bash
./runmyplayer.sh black 60 localhost
```

The three arguments are the side, the move timeout in seconds and the server address.

To set a custom Minimax depth, run the JAR directly and add a fourth argument:

```bash
java -jar TablutINiegghie.jar white 60 localhost 5
```

Valid sides are `white` and `black`. When playing against another client, launch only the side assigned to `INiegghie`.

## Building from source

From the `Tablut` directory, compile the Java sources with Ant:

```bash
cd Tablut
ant clean
ant compile
```

The generated class files are written to `Tablut/build`.

The build file also provides two convenience targets that start a client on `localhost` with a 60-second timeout:

```bash
ant niegghiewhiteclient
```

or:

```bash
ant niegghieblackclient
```

A Tablut server must already be running before either target is launched.

## Project structure

```text
.
├── LICENSE
├── README.md
└── Tablut
    ├── build.xml
    ├── Executables
    │   └── tablut
    │       ├── TablutINiegghie.jar
    │       └── runmyplayer.sh
    ├── lib
    ├── logs
    └── src
        ├── it/unibo/ai/didattica/competition
        │   ├── ai
        │   │   ├── client
        │   │   ├── decisionmaking
        │   │   ├── geneticAlgorithm
        │   │   ├── model
        │   │   └── utility
        │   ├── domain
        │   ├── exceptions
        │   └── util
        └── test/ai
```

The main entry point is `TablutNiegghieClient`. The `decisionmaking` package contains the Minimax search and heuristic evaluation, while `StateDecorator` and `TablutUtility` handle move generation, board inspection and state transitions. The `domain` package contains the game representation and Ashton Tablut rules used by the search.

## Tests

JUnit tests are included for coordinates, directions, state handling, move generation, the heuristic and the Minimax implementation. Test dependencies are stored in the `lib` directory.

## License

This repository includes code distributed under the BSD 3-Clause License. See [`LICENSE`](LICENSE) for details.
