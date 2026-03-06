[![Active Development](https://img.shields.io/badge/Maintenance%20Level-Actively%20Developed-brightgreen.svg)](https://gist.github.com/cheerfulstoic/d107229326a01ff0f333a1d3476e068d)
[![CI Test Status](https://github.com/Adalfarus/chronix/actions/workflows/test-package.yml/badge.svg)](https://github.com/Adalfarus/chronix/actions)
[![License: LGPL-2.1](https://img.shields.io/github/license/Adalfarus/chronix)](https://github.com/Adalfarus/chronix/blob/main/LICENSE)
[![PyPI Downloads](https://static.pepy.tech/badge/chronix)](https://pepy.tech/projects/chronix)
![coverage](https://raw.githubusercontent.com/Adalfarus/chronix/refs/heads/main/coverage-badge.svg)

# chronix

chronix is every concept timer you've ever seen, rolled into one. It has all the helpful features you could ever want.

## Compatibility
🟩 (Works perfectly); 🟨 (Untested); 🟧 (Some Issues); 🟥 (Unusable)

| OS                       |   |
|--------------------------|---|
| Windows                  | 🟩 |
| MacOS                    | 🟩 |
| Linux (Ubuntu 22.04 LTS) | 🟩 |

## Features

- Easy to use for beginners, but not lacking for experts
- Efficient
- Fully cross-platform
- Regular updates and support
- Comprehensive documentation

## Installation

You can install chronix via pip:

```sh
pip install chronix --pre --upgrade
```

Or clone the repository and install manually:

```sh
git clone https://github.com/Adalfarus/chronix.git
cd chronix
python -m pip install .
```

If you have problems with the package please use `py -m pip install chronix[cli,dev] --pre --upgrade --user`

## 📦 Usage

The examples below use the public API exactly as implemented in this repository.

### Basic elapsed time (`BasicTimer`)

```python
import time
from chronix import BasicTimer

timer = BasicTimer(auto_start=True)
time.sleep(0.2)
timer.stop()

print(timer.get())            # datetime.timedelta(...)
print(timer.get_readable())   # "0.2 seconds, ..."
```

### Split/lap-like checkpoints with averages (`BasicTimer`)

```python
import time
from chronix import BasicTimer

timer = BasicTimer().start()
time.sleep(0.05)
timer.split_start()  # start -> now
time.sleep(0.08)
timer.split_end()    # last stop/start -> now

print(timer.get_times())  # [(start, split1), (split1, split2)]
print(timer.tally())      # total seconds as float
print(timer.average())    # average segment as timedelta
```

### Precise delta parsing and formatting (`PreciseTimeDelta`)

```python
from chronix import PreciseTimeDelta, PreciseTimeFormat

delta = PreciseTimeDelta.parse_timedelta_string("01:02:03.4")
print(delta.nanoseconds())                      # 3723400000000.0
print(delta.to_readable(PreciseTimeFormat.SECONDS))
print(delta.to_clock_string())                  # clock-style representation
```

### Flexible timer with explicit start/stop (`FlexTimer`)

```python
from chronix import FlexTimer

t = FlexTimer(start_now=False)
t.start(start_at=1.25)  # pre-load elapsed time
t.wait(0.2)
t.lap()                 # record a lap
t.stop()

print(t.get().to_readable())
print(t.show_laps())
```

### Track multiple timers by index (`FlexTimer`)

```python
from chronix import FlexTimer

t = FlexTimer(start_now=False)
t.start(0, 1)            # start two independent timers
t.wait_ms(20)
t.stop(0)
t.wait_ms(10)
t.stop(1)

print(t.get(0).to_readable())  # shorter
print(t.get(1).to_readable())  # longer
```

### Decorator timing for functions

```python
from chronix import FlexTimer

@FlexTimer.time()
def build_values(n: int) -> list[int]:
    return [i * i for i in range(n)]

build_values(100_000)
```

### Context manager timing for code blocks

```python
from chronix import CPUFTimer

with CPUFTimer():
    sum(i * i for i in range(200_000))
```

`CPUFTimer` uses process CPU time, so sleep/wait time is mostly excluded.

### Schedule delayed and repeated callbacks

```python
from chronix import FlexTimer

timer = FlexTimer(start_now=False)

timer.after(1.0, print, args=("one-shot fired",))
timer.interval(0.5, 3, print, args=("interval fired",))
timer.schedule_task_at("23:59", print, args=("scheduled for clock time",))
```

### Long-running loop you can stop manually

```python
from chronix import FlexTimer

timer = FlexTimer(start_now=False)
timer.interval(0.25, "inf", print, args=("tick",))

# ... later
timer.stop_loops(0)
```

### Estimate time complexity of a function

```python
from chronix import FlexTimer

def work(n: int) -> int:
    return sum(range(n))

def inputs():
    for n in range(1_000, 20_000, 1_000):
        yield ((n,), {})

print(FlexTimer.complexity(work, inputs()))  # e.g. "O(N)"
```

`FlexTimer.complexity` requires optional dependencies: `numpy`, `scipy`, and `scikit-learn`.

### CLI examples

Install CLI extras if needed:

```sh
pip install "chronix[cli]"
```

Show help:

```sh
chronix help
```

Run all tests from the CLI:

```sh
chronix tests run
```

Run selected tests in minimal mode:

```sh
chronix tests run tests -minimal
```

For more detailed usage and examples, check out our [documentation](https://github.com/adalfarus/chronix/wiki).

## Naming convention, dependencies and library information
[PEP 8 -- Style Guide for Python Code](https://peps.python.org/pep-0008/#naming-conventions)

For modules I use 'lowercase', classes are 'CapitalizedWords' and functions and methods are 'lower_case_with_underscores'.

## Contributing

We welcome contributions! Please see our [contributing guidelines](https://github.com/adalfarus/chronix/blob/main/CONTRIBUTING.md) for more details on how you can contribute to chronix.

1. Fork the repository
2. Create your feature branch (`git checkout -b feature/AmazingFeature`)
3. Commit your changes (`git commit -m 'Add some AmazingFeature'`)
4. Push to the branch (`git push origin feature/AmazingFeature`)
5. Open a pull request

### Aps Build master

You can use the aps_build_master script for your os to make your like a lot easier.
It supports running tests, installing, building and much more as well as chaining together as many commands as you like.

This example runs test, build the project and then installs it
````commandline
call .\aps_build_master.bat 234
````

````shell
sudo apt install python3-pip
sudo apt install python3-venv
chmod +x ./aps_build_master.sh
./aps_build_master.sh 234
````

## License

chronix is licensed under the LGPL-3.0 License - see the [LICENSE](https://github.com/adalfarus/chronix/blob/main/LICENSE) file for details.

