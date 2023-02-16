# Python
```python
import re

if __name__ == '__main__':
    str = "(1,2,3,4),(4,5,6,7)"

    splited = re.split("\(|\)", str)

    arr = []
    for s in splited:
        if len(s) > 0 and s != ",":
            print(s)
```
