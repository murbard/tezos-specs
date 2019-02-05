# Tendermint-like consensus for Tezos

# Principle

As in the alpha protocol, for a given block height, there is a priority list of proposers
and a set of endorsers. The size of the set of endorsers is increased from 32 to 127.
For a block to be valid, it needs to include at least 85 endorsements for its predecessor,
unless its predecessor is a recovery block.

As in Tendermint, we proceed in rounds of proposals / endorsements (pre-commits). However,
unlike Tendermint, past a certain round, blocks are considered recovery blocks and need
not be endorsed. This is used to *recover* from a Byzantine set of endorsers.

The presence of recovery blocks mean we lose finality and we require a synchronous.
Why would we want them?

1. The time delay before recovery blocks are produced can be very large, making
   concerns about synchronicity moot. At this time scal,e synchronicity issues will
   only arise out of non-trivial (and easily detectable) network partitions, not byzantine behavior.
   Think New Zealand being cut-off from the Internet, not Denial Of service.
   
2. Participants concerned about finality can wait until finalized blocks are produced again.
   Since the endorsers set are chosen weeks in advance, seeing a finalized block is enough
   to confirm that the chain has converged again, unless the recovery blocks somehow go on
   for weeks.

3. Softening the requirements lets us recover gracefully from an unlucky draw of endorsers. This
   means the set of endorsers can be much smaller than it otherwise would be.
   
# Round schedule

Let DT[0] = 15 seconds be the sum of all timeouts for round 0. This means that the 
proposal for the round 1 can be produced at time t + DT. In general, let DT[i] be the
total timeout for round i, DT[0] = 15s, DT[1] = 15s, DT[2] = 15s, DT[n+3] = DT[n] + DT[n+1].

This implements, in the integers, an exponential backoff where the timeout increases by about
32% at each round.

The proposal timeout, prevote timeout and precommit timeout are divided equally in the schedule.

Given this schedule, round 20 starts 50 minutes after the previous block. Proposals made
from round 20 and onward are considered recovery blocks.

# Economics

Switching to % of money supply as opposed to fixed token amounts.

Given that:
 - We can't predict the average block time beforehand
 - We would like to target an inflation rate based on time, not block height
 - We don't want to induce weird endorsement strategy to change the reward
 
 The solution is to determine the reward based on a slow moving average of
 block times.
 
Consider an exponential moving average of time between blocks with a characteristic
time of 30 calendar days, divide it by one calendar year, multiply by log(1.055),
subtract 1 and multiply that by the current circulating supply. This gives
the total maximum reward for the block. All of this can be done easily with interger arithmetic only.

The maximum reward for the block is then modified to take into account the number of endorsements.

If the block is a recovery block, only 10% of the reward is issued and it is payed to the baker.
Otherwise, the total reward is reduced linearly from 100% of its value to 80% of its value as
the number of endorsers go from 127 to 85.

Finally, the reward is divided as follow: 19% for the block produced, 76% for the endorsers, and 5%
to tip the revelation of nonces or pvss shares.



