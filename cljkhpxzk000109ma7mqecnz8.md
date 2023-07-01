---
title: "@pytest.mark.parametrize: Enhancing clarity with "wrapped" parametrized arguments"
datePublished: Sat Jul 01 2023 21:03:17 GMT+0000 (Coordinated Universal Time)
cuid: cljkhpxzk000109ma7mqecnz8
slug: pytestmarkparametrize-enhancing-clarity-with-wrapped-parametrized-arguments
ogImage: https://cdn.hashnode.com/res/hashnode/image/upload/v1688245374556/e6a7ad51-267f-48f5-a946-ba053b62d471.png
tags: python, pytest

---

One of the Pytest features I like and use most is [`@pytest.mark.parametrize`](https://docs.pytest.org/en/7.3.x/how-to/parametrize.html). Below is an example of how to use it (from [Pytest documentation](https://docs.pytest.org/en/7.3.x/how-to/parametrize.html#pytest-mark-parametrize-parametrizing-test-functions)):

```python
import pytest


@pytest.mark.parametrize("test_input,expected", [
    ("3+5", 8), ("2+4", 6), ("6*9", 42)
])
def test_eval(test_input, expected):
    assert eval(test_input) == expected
```

### Scenario

For tests where there are one or two parametrized arguments only, the names and values for the arguments are easily readable. However, the complexity can increase when there are several parametrized arguments and, even more when [fixtures](https://docs.pytest.org/en/6.2.x/fixture.html#back-to-fixtures) are passed to the test function as well. In such cases, it is not immediate to map the value to the argument for a given input when reading the code. In the example below we can notice that the test signature is longer (as expected) and there is no clear distinction between the parametrized arguments and the fixtures passed in.

```python
import pytest

@pytest.fixture()
def dummy():
    # Dummy fixture
    return


def eval_math_operation(x: int, operator: str, y: int) -> int:
    # Dummy function just for illustration purposes
    return eval(f"{x} {operator} {y}")


@pytest.mark.parametrize("x, operator, y, expected", (
        (1, "+", 2, 3),
        (1, "-", 2, -1),
        (1, "*", 2, 2),
        (1, "/", 2, 0.5),
))
def test_eval_math_operation(x, operator, y, expected, dummy):
    assert eval_math_operation(x, operator, y) == expected
```

### An alternative approach

To enhance clarity on the inputs of the tests, I usually prefer to wrap each set of parametrized arguments into a dictionary so "what would be" the parametrized arguments becomes a single one.

```python
import pytest 

@pytest.fixture()
def dummy():
    # Dummy fixture
    return


def eval_math_operation(x: int, operator: str, y: int) -> int:
    # Dummy function just for illustration purposes
    return eval(f"{x} {operator} {y}")

@pytest.mark.parametrize("test_case", (
        dict(x=1, operator="+", y=2, expected=3),
        dict(x=1, operator="-", y=2, expected=-1),
        dict(x=1, operator="*", y=2, expected=2),
        dict(x=1, operator="/", y=2, expected=0.5),
))
def test_eval_math_operation(test_case, dummy):
    assert eval_math_operation(
        x=test_case["x"],
        operator=test_case["operator"],
        y=test_case["y"]
    ) == test_case["expected"]
```

That way, it is easier to understand not only the arguments in each test case but also the test signature, wherein it is now clear the distinction between the single test's parametrized argument and any additional fixtures. The test runner doesn't complain and the others reading the code may thank you! 😄