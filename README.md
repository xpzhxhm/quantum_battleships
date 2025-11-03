# Quantum_Battleships
A quantum version of the traditional battleship game

## Overview

We made a **quantum version of Battleships** that you can play in a Jupyter Notebook.
You have two actions on any grid square:

- **Scan (quantum):** Use a small quantum circuit to “peek” without fully looking.
- **Fire (classical):** Try to hit a ship.

The scan uses **EV bomb testing** ideas (interaction-free hints) and the **Quantum Zeno effect** (many tiny checks).

## How to Use

### The main page of our game

There're several parts on our UI. 

On the top-left corner, there's the board of AI, where you can select a square, choose an action mode and adjust the parameters. 

On the bottom-left corner, there's your board. You can see your ships and AI's movement here. 

Once you select a square on AI's board, you can choose an action mode and adjust the parameters on the right of the page, then press 'Confirm! '. The results (hit/miss/found without exploded/exploded) will shown in the 'Stats' block. 

After you took an action, please press 'AI Move' bottom, then AI will choose square/action mode/parameters and action. 

![截屏2025-10-31 20.16.14](/Users/vincent/Desktop/截屏2025-10-31 20.16.14.png)

There are 4 symbols in the game: 

``` python
    symbols = {
        "O": " ",        # white label means the grid is never scanned or fired. 
        "X": "❌",       # means there is a ship hitted in the grid. 
        "M": "⭕️",       # means the grid is fired, but there's no ship here. 
        "S": "🟨",       # means the grid is scanned, and there's probably a ship. 
        "NS": "🟩",      # means the grid is scanned, and there's not likely a ship. 
    }
```

After an action, you'll be able to see the page like:

![截屏2025-10-31 20.17.53](/Users/vincent/Desktop/截屏2025-10-31 20.17.53.png)

Your and AI's scores are shown on the right side of the string 'AI' and 'You'. That score is calculated using measurement times, hits, misses, etc. The detailed explanation of how to calculate the score is on the **Score&Cost** part. 

A statistic log is printed below your grid. That's 2 dictionary like:

``` python
    usage = {
        'shots': 0,
        'measuments': 0,
        'positives': 0,
        'explosions': 0,
        'fire times': 0,
        'hits': 0,
        'misses': 0,
    }
```

## Code: Quantum Engine

**One qubit only**. 

From the outcomes we label each scan as:

- **found** (dark event seen) → likely ship,

- **explodes** (rare penalty event), or

- **no_ship**.

  

  `QuantumEngine1` is the **quantum scanning engine** used in the Quantum Battleships game.
  It checks a board square using a **Quantum Zeno / EV-style probe** to guess whether a ship is there **while spending as few resources as possible**

### How does it work?

- It normally uses **Qiskit + AerSimulator** to run a **single-qubit circuit**.
- The probe applies **many tiny rotations**.
  If a ship is present, the engine performs **mid-circuit measurements + reset** each step, then one final readout.
- It counts two important types of events:
  - **dark** → hint that a ship is present
  - **exploded** → rare but a stronger sign of a ship (and gives a penalty)
- It also calculates a **Wilson confidence interval** to show how confident we are about “ship present”.

### Key methods

| Method                     | Purpose                                                      |
| -------------------------- | ------------------------------------------------------------ |
| `_zeno_counts_qiskit(...)` | Runs the real quantum circuit and returns statistics (dark, exploded, counts). |
| `_zeno_counts_proxy(...)`  | A simple probability-based fallback if Qiskit/Aer is not available. |
| `_wilson_ci(...)`          | Gives a confidence range for the probability of “ship found”. |
| `probe(...)`               | **Main public method**: scans a board square and returns results + cost + UI stats. |

## Code: AI

In AI Strategy part, we use Bayesian Belief Algorithm to improve its E.V. Score. First, we choose target among max-prob untargeted cells.Second, we consider the neighborhood of it and use Suggest method to adjust the next target. Finally, use update method to update the coordinate to choose for next step. We use In-place bayesian-like local update (reference rules) to update the label. After that, the next action depends on the status, that is whether Explodes, Found or Miss. If miss, continue to search for the next position. If explodes, Fire directly at the same position. If Found, continue to search around it.

## Code: Other Classes

### Board

Board state
The class holds two 10×10 grids—one for the opponent and one for the player—plus two lists of ship coordinate sets for each side.

### Game

Class Game shows the flow line of how ships are scaned or fired.The method ai_take_turn shows how AI will take strategy to improve its ev score.

### UI

Finally, when it runs, we defind the functions on_scan and on_fire to show how it is demonstrated by the User Interface. Then display the UI.
