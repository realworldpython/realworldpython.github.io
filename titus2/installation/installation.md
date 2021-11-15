## Requirements

Titus 2 uses a number of open source projects to work properly:

* `avro-python3 >= 1.8.2`
* `numpy >= 1.15.0`
* `pytz >= 2019.1`
* `pyyaml >= 5.1.2`
* `ply >= 3.11`

The above packages are available via `pip3` and are automatically installed during setup.

## Installation

Titus 2 requires Python 3.4+ to run. The latest version of Python 3 can be downloaded from the [official website](https://www.python.org/download/){: target=_blank }. 
It can be installed via pip3 as follows:
```sh
pip3 install titus2
```

or you can directly install the developer version from github repository via 
```sh
pip3 install git+https://github.com/animator/titus2.git
```

!!! attention "Attention"
    The library is named `titus2` as it is a fork which is not maintained by the original developers of the `titus` library. To an end user, the module is still accessible as `titus` to ensure compatibilty across Python 2.x and 3.x codes. 

## Verify Installation

To confirm your installation please run the following elementary example in python:

```python
from titus.genpy import PFAEngine

pfa = {"input": "double",
 "output": "double",
 "action": [
   {"+": ["input", 100]}
 ]}
engine, = PFAEngine.fromJson(pfa)

l = [1.0, 2.0, 3.0, 4.0, 5.0]

for num in l:
    print(num, engine.action(num))
```

!!! bug "Error"
    If you are getting any error message please contact the author via [e-mail](mailto:ankit@realworldpython.github.io) or raise an issue [here](https://github.com/animator/titus2/issues){: target=_blank }.
