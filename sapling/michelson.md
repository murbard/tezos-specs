# Michelson integration of Sapling

This is a low granularity integration of Sapling in Michelson

## Dazzle

We introduce the datatype `dazzle`. Under the hood, a dazzle represents

1. a nullifier set
2. a dequeue of merkle trees (sharing nodes)
3. a map between root hashes and the corresponding merkle tree in the dequeue

Dazzles are constructed using the opcode

```
MAKE_DAZZLE

:: nat : 'S -> dazzle : 'S

```

Which creates a dazzle and sets the maximum length of the dequeue.

Dazzles are just handles. Much like bigmaps, they are not fully deserialized when accessed.

## Operations

### `SHIELD`

```
SHIELD

:: nat : note : proof : dazzle : 'S   ->  option dazzle : 'S
```

Attempts to create a note and insert it inside the dazzle.

### `UNSHIELD`

```
UNSHIELD

:: nat : root_hash : proof : dazzle : 'S -> option dazzle : 'S
```

attempts to spend a note present in the merkle tree with the hash root_hash

### `SHIELDED_TX`

```
:: root_hash : list (pair (note, proof)) : list (pair (note, proof)) : proof : dazzle : 'S  ->  option dazzle : 'S
```
