# Statistical Verification Notes

This document records a large-sample verification pass for `RandomGen`, checking that generated values follow the configured probability distribution within an accepted tolerance.

## Test design

- Generated `10,000` randomized probability distributions.
- Ran `10,000` draws per distribution.
- Total sampled draws: `100,000,000`.
- Generated integer values across the allowed range of `-1,000,000,000` to `1,000,000,000`.
- Normalized random probabilities so each distribution summed to `1.0`.
- Compared actual draw counts against expected counts.
- Accepted a `2%` variance.

## Result

The generated verification run passed with no assertion failures. Observed frequencies stayed within the configured tolerance for the sampled distributions.

## Why this is not part of the default test suite

This check is intentionally documented rather than included in the normal unit test command because it is slow and stochastic. The committed unit tests focus on deterministic behavior, validation rules, duplicate handling, and binary-search selection boundaries.

## Verification script used

```python
def test_random_gen(self):
    test_count = 0

    max_amount_of_numbers = RandomGen._MAX_LIST_LENGTH
    max_number = RandomGen._MAX_ALLOWED_INT

    for _ in range(10000):
        test_count += 1

        num_numbers = randint(1, max_amount_of_numbers)
        numbers = sample(range(-max_number, max_number), num_numbers)
        random_probabilities = [random() for _ in range(num_numbers)]
        total_prob = sum(random_probabilities)
        probabilities = [prob / total_prob for prob in random_probabilities]

        random_gen = RandomGen(numbers, probabilities)

        num_trials = 10000
        results = defaultdict(int)
        for _ in range(num_trials):
            result = random_gen.next_num()
            results[result] += 1

        for number, prob in zip(numbers, probabilities):
            expected_count = prob * num_trials
            actual_count = results[number]
            tolerance = 0.02 * num_trials

            assert expected_count - tolerance <= actual_count <= expected_count + tolerance, (
                f"Test {test_count}: Number {number} was expected to appear "
                f"about {expected_count} times but appeared {actual_count} times."
            )
```
