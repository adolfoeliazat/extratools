[Source](https://github.com/chuanconggao/extratools/blob/master/extratools/dicttools.py)

## Dictionary Inverting

Tools for inverting dictionaries.

### `invert`

`invert(d)` inverts `(Key, Value)` pairs to `(Value, Key)`.

- If multiple keys share the same value, the inverted directory keeps last of the respective keys.

``` python
invert({1: 'a', 2: 'b', 3: 'c'})
# {'a': 1, 'b': 2, 'c': 3}
```

### `invert_safe`

`invert_safe(d)` inverts `(Key, Value)` pairs to `(Value, List[Key])`.

- If multiple keys share the same value, the inverted directory keeps a list of all the respective keys.

``` python
d = {1: 'a', 2: 'b', 3: 'a'}

invert(d)
# {'a': 3, 'b': 2}

invert_safe(d)
# {'a': [1, 3], 'b': [2]}
```

### `invert_multiple`

`invert_multiple(d)` inverts either `(Key, Value)` to `(Value, Key)`, or `(Key, Iterable[Value])` pair to multiple `(Value, Key)`.

- If multiple keys share the same value, the inverted directory keeps last of the respective keys.

``` python
invert_multiple({1: 'abc', 2: 'def', 3: 'ghi'})
# {'a': 1, 'b': 1, 'c': 1, 'd': 2, 'e': 2, 'f': 2, 'g': 3}
```

## Remapping

Tools for remapping elements.

### `remap`

`remap(data, mapping, key=None)` remaps each unique element in `data` to a new value from calling function `key`.

- `mapping` is a dictionary recording all the mappings, optionally containing previous mappings to reuse.

- In default, `key` returns integers starting from `0`.

``` python
docs = [
    ['a', 'b', 'c', 'd', 'e'],
    ['b', 'b', 'b', 'd', 'e'],
    ['c', 'b', 'c', 'c', 'a'],
    ['b', 'b', 'b', 'c', 'c']
]

wordmap = {}
db = [list(remap(doc, wordmap)) for doc in docs]

db
# [[0, 1, 2, 3, 4],
#  [1, 1, 1, 3, 4],
#  [2, 1, 2, 2, 0],
#  [1, 1, 1, 2, 2]]

wordmap
# {'a': 0, 'b': 1, 'c': 2, 'd': 3, 'e': 4}
```

## Indexing

Tools for indexing.

### `invertedindex`

`invertedindex(seqs)` creates an [inverted index](https://en.wikipedia.org/wiki/Inverted_index).

- Each item's index is a list of `(ID, position)` pairs for all the sequences in `seqs` containing the item.

``` python
data = [s.split() for s in [
    "a b c d e",
    "b b b d e",
    "c b c c a",
    "b b b c c"
]]

invertedindex(data)
# {'a': [(0, 0), (2, 4)],
#  'b': [(0, 1), (1, 0), (2, 1), (3, 0)],
#  'c': [(0, 2), (2, 0), (3, 3)],
#  'd': [(0, 3), (1, 3)],
#  'e': [(0, 4), (1, 4)]}
```

### `nextentries`

`nextentries(data, entries)` scans the sequences in `data` from left to right after current entries `entries`, and returns each item and its respective following entries.

- Each entry is a pair of `(ID, Position)` denoting the sequence ID and its respective matching position.

``` python
# same data from previous example

# the first positions of `c` among sequences.
entries = [(0, 2), (2, 0), (3, 3)]

nextentries(data, entries)
# {'d': [(0, 3)],
#  'e': [(0, 4)],
#  'b': [(2, 1)],
#  'c': [(2, 2), (3, 4)],
#  'a': [(2, 4)]}
```

## Dictionary Flatten/Unflatten

Tools for flatten/unflatten a dictionary.

### `flatten`

`flatten(d, force=False)` flattens a dictionary by returning `(Path, Value`) tuples with each path `Path` from root to each value `Value`.

- For each path, if any array with nested dictionary is encountered, the index of the array also becomes part of the path.

- In default, only an array with nested dictionary is flatten. Instead, parameter `force` can be specified to flatten any array.

!!! warning
    Different from [`jsontools.flatten`](jsontools/#flatten), this function accepts only dictionary.

    An empty dictionary disappears after being flatten. When use `force = True`, an empty array disappears after being flatten.

``` python
flatten(json.loads("""{
  "name": "John",
  "address": {
    "streetAddress": "21 2nd Street",
    "city": "New York",
  },
  "phoneNumbers": [
    {
      "type": "home",
      "number": "212 555-1234"
    },
    {
      "type": "office",
      "number": "646 555-4567"
    }
  ],
  "children": [],
  "spouse": null
}"""))
# {'name': 'John',
#  ('address', 'streetAddress'): '21 2nd Street',
#  ('address', 'city'): 'New York',
#  (('phoneNumbers', 0), 'type'): 'home',
#  (('phoneNumbers', 0), 'number'): '212 555-1234',
#  (('phoneNumbers', 1), 'type'): 'office',
#  (('phoneNumbers', 1), 'number'): '646 555-4567',
#  'children': [],
#  'spouse': None}
```

