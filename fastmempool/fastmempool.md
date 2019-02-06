# Fast mempool

General idea: separate the concept of *applying* a block and determining the *validity* of a block.
With some tweaks, the validity of a block can be asserted much faster than its effects can be computed.

## Current situation

Smart-contracts are executed by every node three times
 - when entering the mempool
 - when selected by the baker
 - when applied as part of a block

## Improvement

Most operations are fast to validate, the exception being potentially manager operations which can
trigger smart contracts. The validity of manager operations can be asserted quickly by verifying a
signature, and ensuring that the transaction fees can be paid.

The idea: enforce that, for a block to be valid, all fees paid in all manager's operations must be payable based on the balance
of the manager's implicit accounts at the block before.

Blocks can be relayed if they are valid, without being fully executed.

For this to be possible, the context hash for the result of creating a block must be placed in the next block.

This simplifies the baker's code a great deal as it can grab the best set of transactions from the mempool
and include them in a block without having to worry about cross-interactions.
