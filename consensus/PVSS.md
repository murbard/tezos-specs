# PVSS

For 32 endorsers pick a threshold of 10, commitment every 32 blocks.
Commitments can be revealed in first half of next cycles. If not
revealed, shares can be revealed in the second half and combined.

Primitives already implemented in `lib_crypto`

## Todo

- Attach a secp256k1 group element to every delegate
- Storage for commtiments, shares
- New operation: reveal clear shared
- Change baker 
- Ledger support (not critical initially)
- Change threshold to harmonize with more delegates (127 for tendermint proposal)
