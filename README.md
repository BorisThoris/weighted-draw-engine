# Weighted Draw Engine

Weighted Draw Engine is a small Python algorithm project for drawing integers from a configured probability distribution. It validates the input, merges duplicate numbers by summing their probabilities, builds cumulative probability ranges, and uses binary search to resolve each random draw in `O(log n)` time.

## What it shows

- Probability-distribution modeling without third-party dependencies.
- Defensive input validation for lengths, types, totals, value bounds, and empty inputs.
- Duplicate number handling by merging weights before sampling.
- Binary-search lookup over cumulative probabilities.
- Deterministic unit tests using `unittest.mock.patch`.
- Statistical verification notes from a large generated test run.

## Usage example

```python
from random_number_generator import RandomGen

numbers = [-1, 0, 1, 2, 3]
probabilities = [0.01, 0.3, 0.58, 0.1, 0.01]

random_gen = RandomGen(numbers, probabilities)
print(random_gen.next_num())
```

## Core constraints

- `numbers` must be integers.
- `probabilities` must be floats.
- The probability total must equal `1.0` after rounding to the configured precision.
- Input lists must be non-empty, equal length, and no longer than `1000`.
- Number values must stay within `+/- 10^9`.

## Implementation notes

The implementation converts the configured probabilities into cumulative ranges. For each draw, it generates a random float and uses binary search to find the first cumulative boundary greater than that value.

Python already has high-level alternatives such as `random.choices`, `numpy.random.choice`, and `bisect`. This project keeps the algorithm explicit so the validation rules, duplicate handling, and lookup strategy are easy to inspect.

## Tests

```bash
python -m unittest
```

The unit tests cover validation failures, cumulative probability construction, duplicate values, zero probabilities, single-value distributions, and deterministic `next_num()` behavior.

Additional large-sample verification notes are documented in [Statistical_Defense_ReadMe.md](./Statistical_Defense_ReadMe.md).
