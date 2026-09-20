# Gravity Rush Prototype — Unreal Engine 5 Blueprint

Directional gravity mechanic inspired by *Gravity Rush*, combined with a gravity-based attack. Built in Blueprint as part of an academic UML modelling project at HEAJ (Belgium).

## Overview

The player moves through gravity zones that each apply gravity differently — free control, forced direction, or black-hole pull toward a center point. The player can stick to surfaces and trigger a gravity attack on enemies, timed to land correctly.

## Features

- Directional gravity changed by the player, applied per zone type (`ZoneLibre`, `ZoneImposee`, `TrouNoir`)
- Surface-sticking and controlled falling
- Timing-based gravity attack on enemies
- Zone detection and gravity switching handled independently of player logic

## Class Diagram

```mermaid
classDiagram
    class Player {
        -position: Vector3
        -velocity: Vector3
        -gravityDirection: Vector3
        -isInGravityZone: bool
        +voler()
        +sAccrocher(surface: Surface)
        +changerGravite(direction: Vector3)
        +attaquerGravite(cible: Ennemi)
    }
    class ZoneGravite {
        <<abstract>>
        -directionGravite: Vector3
        -intensiteGravite: float
        +appliquerGravite(player: Player)
        +sortirZone(player: Player)
    }
    class ZoneLibre {
        +controleLibre(player: Player)
    }
    class ZoneImposee {
        +forcerGravite(player: Player)
    }
    class TrouNoir {
        +aspirer(player: Player)
    }
    class Ennemi {
        -pointsDeVie: int
        +subirDegatsGravite()
    }
    class Surface {
        -type: SurfaceType
        +accrocher(player: Player)
    }

    ZoneGravite <|-- ZoneLibre
    ZoneGravite <|-- ZoneImposee
    ZoneGravite <|-- TrouNoir
    Player ..> ZoneGravite : entre / sort
    Player ..> Ennemi : attaque
    Player ..> Surface : interaction
```

## Sequence — Gravity Attack Cycle

```mermaid
sequenceDiagram
    participant Joueur
    participant PlayerCharacter
    participant ZoneGravite
    participant Surface
    participant Ennemi

    PlayerCharacter->>ZoneGravite: checkOverlap()
    ZoneGravite-->>PlayerCharacter: typeZone
    alt ZoneLibre
        PlayerCharacter->>ZoneGravite: controleLibre()
    else ZoneImposee
        PlayerCharacter->>ZoneGravite: forcerGravite()
    else TrouNoir
        PlayerCharacter->>ZoneGravite: aspirer()
    else Aucune
        PlayerCharacter->>PlayerCharacter: graviteNormale()
    end

    PlayerCharacter->>Surface: detecterSurface()
    alt Surface trouvée
        PlayerCharacter->>Surface: sAccrocher()
    else Aucune surface
        PlayerCharacter->>PlayerCharacter: chuteControlee()
    end

    Joueur->>PlayerCharacter: inputAttaque()
    PlayerCharacter->>Ennemi: attaquerGravite(cible)
    PlayerCharacter->>PlayerCharacter: verifierTimingAttaque()
    alt Timing correct
        PlayerCharacter->>Ennemi: subirDegats(1)
    else Timing incorrect
        PlayerCharacter->>PlayerCharacter: attaqueEchouee()
    end
```

## Decision Logic

```mermaid
flowchart TD
    A[Player in gravity zone?] -->|No| B[Free normal gravity control]
    A -->|Yes| C{Zone type}
    C -->|ZoneLibre| D[Player controls gravity freely]
    C -->|ZoneImposee| E[Gravity forced on player]
    C -->|TrouNoir| F[Pull player toward zone center]
    D --> G[Apply movement & physics]
    E --> G
    F --> G
    B --> G
    G --> H[Check surface collision]
    H -->|Collision| I[Stick or bounce off surface]
    H -->|No collision| G
```

## Tech Stack

- Unreal Engine 5 (Blueprint)
- UML modelling (use case, class, sequence, activity diagrams)

## Author

**Loan Dzelili** — [loan-gamedev.github.io](https://loan-gamedev.github.io)
