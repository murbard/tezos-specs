# Michelson integration of Sapling

This is a low granularity integration of Sapling in Michelson

## Dazzle

We introduce the datatype `dazzle`. Under the hood, a dazzle represents

1. a nullifier set
2. a dequeue of merkle trees (sharing nodes) (can simply be implemented as a map from indices
to merkle trees)
3. a map between root hashes and the corresponding merkle tree in the dequeue

Dazzles are constructed using the opcode

```
MAKE_DAZZLE

:: nat : 'S -> dazzle : 'S

```

Which creates a dazzle and sets the maximum length of the dequeue.

Dazzles are just handles. Much like bigmaps, they are not fully deserialized when accessed.

## Types

- `dazzle` described above
- `proof` not sure if it's worth separating each proof with a specific datatype
or if they can all be "proofs". If the wrong type of proof is passed, it can
be treated as an invalid proof.
- `note` a node that is to be placed in the dazzle

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

## Even less granular

We could also ignore notes, proofs etc and further encapsulate with


```
SHIELD

:: nat : shield_op : dazzle : 'S -> option dazzle

UNSHIELD

:: unshield_op : dazzle : 'S -> nat : dazzle : 'S

SHIELDED_TX

:: shielded_tx_op : dazzle : 'S -> dazzle : 'S
```