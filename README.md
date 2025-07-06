# Binary Struct Parser is a script that reads a binary file, breaks it into fixed entries (e.g. 5 integers, 1 floating point number, a string of 255 bytes), decodes the fields and assembles them into convenient dictionaries for analysis. Based on approaches from StackOverflow on using struct.unpack() in a loop

## Example of use:

```python
import struct
from functools import partial

FORMAT = '=5if255s'
struct_len = struct.calcsize(FORMAT)
struct_unpack = struct.Struct(FORMAT).unpack_from

def read_chunks(f, length):
    while True:
        chunk = f.read(length)
        if not chunk:
            break
        yield chunk

def parse_binary_file(filename):
    results = []
    with open(filename, 'rb') as f:
        for chunk in read_chunks(f, struct_len):
            year, a, b, c, d, pop, name_bytes = struct_unpack(chunk)
            name = name_bytes.split(b'\0', 1)[0].decode('utf-8', errors='ignore')
            results.append({
                'year': year,
                'values': (a, b, c, d),
                'population': pop,
                'name': name
            })
    return results

if __name__ == '__main__':
    data = parse_binary_file('metro_areas.bin')
    for rec in data[:5]:
        print(rec)
```

The basis of the method is struct.unpack() in a while loop that reads data from a file for now - the most idiomatic way to parse binary records
