# Repository Guidelines

## Project Structure & Module Organization
The repository is intentionally small:

- `Quantum_Battleship_Zeno_fixed-5.ipynb`: the full application. It contains the data models, board logic, quantum engine, AI strategy, ipywidgets UI, and the driver cell.
- `README.md`: user-facing overview and gameplay notes.
- `LICENSE`: project license.

There is no separate `src/`, `tests/`, or assets directory yet. If you split notebook code into modules later, keep runtime code under `src/` and mirror it with tests under `tests/`.

## Build, Test, and Development Commands
Work from the repository root.

- `jupyter notebook Quantum_Battleship_Zeno_fixed-5.ipynb`: open the game notebook locally.
- `python3 -m jupyter nbconvert --execute --to notebook --inplace Quantum_Battleship_Zeno_fixed-5.ipynb`: execute the notebook end to end as a regression check.
- `python3 -m jupyter nbconvert --clear-output --inplace Quantum_Battleship_Zeno_fixed-5.ipynb`: remove transient cell output before committing.

Install dependencies in your environment before running the notebook. The notebook currently expects packages such as `numpy`, `ipywidgets`, `qiskit`, and `qiskit-aer`.

## Coding Style & Naming Conventions
Use Python conventions throughout notebook cells:

- 4-space indentation, no tabs.
- `PascalCase` for classes (`QuantumEngine1`, `BayesianBeliefAI`).
- `snake_case` for functions, methods, variables, and widget handlers (`ai_take_turn`, `on_scan`).
- Keep cells focused by topic: imports/rules, board, engine, game, AI, UI, driver.

Prefer small, reviewable edits. When changing behavior, update both the notebook and any matching explanation in `README.md`.

## Testing Guidelines
There is no automated test suite yet. For every change:

- Run the notebook from a clean kernel.
- Verify one player scan, one player fire, and one AI move.
- Check that score, board symbols, and widget updates still match expected gameplay.

If you add tests, use `pytest`, place them in `tests/`, and name files `test_<feature>.py`.

## Commit & Pull Request Guidelines
Recent commits use short, imperative subjects, for example: `Update README.md` and `Remove image references from README.md`. Follow that pattern.

PRs should include:

- A brief summary of behavior changed.
- Any dependency or environment changes.
- Screenshots or short notes for UI changes in the notebook.
- Confirmation that the notebook was executed successfully from a clean kernel.
