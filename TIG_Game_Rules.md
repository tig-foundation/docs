# TIG Game Rules

Version 2.0, March 2024

The features, operation and functionality of The Innovation Game is defined by the source code and config (together the "**Specification**") which can be viewed at:

* **Code:** https://github.com/tig-foundation/tigpy
* **Latest Config:** https://api.tig.foundation/v1/getBlock

For the purposes of determining each party’s benefits and obligations under the TIG Rules, in the event of any conflict or inconsistency between the TIG Rules and the Specification, the Specification shall prevail.

## Rules for Playing The Innovation Game

### Rules for Innovators

1. Before playing The Innovation Game as an Innovator you must read and accept the [Terms and Conditions](TIG_Terms_And_Conditions.pdf).

2. To submit an algorithm to The Innovation Game you must first burn a fee ("**Submission Fee**""). The Submission Fee is determinable from the [latest config](https://api.tig.foundation/v1/getBlock).

3. Innovators must make a meaningful improvement to any algorithm licensed under the TIG Innovator Outbound Game License before submitting the modified algorithm to The Innovation Game.

4. Algorithms submitted to the Innovation Game must be submitted in accordance with the TIG IP Policy:

    https://github.com/tig-foundation/docs/blob/main/TIG_IP_Policy.pdf 

### Rules for Benchmarkers

1. Before playing The Innovation Game as a Benchmarker you must read and accept the [Terms and Conditions](TIG_Terms_And_Conditions.pdf).

2. Only algorithms licensed under the TIG Benchmarker Outbound Game License may be used for benchmarking in The Innovation Game. Benchmarkers must generate and submit a runtime signature along with any solutions found:

    https://github.com/tig-foundation/challenges 

3. Solutions submitted by Benchmarkers will be random sampled and verified by AWS lambdas. Your solution must not take more than 1024MB of memory or take longer than 5s to finish. If your solution does not satisfy either of those criteria then the solution will be rejected.

## The Innovation Game Obligations to You

### Rounds

1. "**Round**" means an elapsed period of time required for the creation of a specified, discrete number of blocks in The Innovation Game. The relevant elapsed period of time for the current round is estimable from the [latest config](https://api.tig.foundation/v1/getBlock).

2. Each Round of the Innovation Game will be assigned a consecutive number in sequence.

### Rewards

1. For the current round, the number of TIG tokens available as rewards per block is determinable from the [latest config](https://api.tig.foundation/v1/getBlock).

### Rewards for Benchmarkers

1. For the current round, the percentage of rewards available for Benchmarkers is determinable from the [latest config](https://api.tig.foundation/v1/getBlock).

2. The Innovation Game rewards Benchmarkers based on their average percentage of qualifiers across challenges. If a Benchmarker has different percentage of qualifiers across challenges an imbalance penalty based on standard deviation of a Benchmarker's percentage of qualifiers across challenges will be applied.

3. Rewards earned by Benchmarkers in Rounds 1 to 13 inclusive will be transferable by the Benchmarker within five (5) working days after end of Round 13 and rewards earned in each subsequent Round, will be transferable within five (5) working days after the end of each such Round.


### Rewards for Innovators

1. For the current round, the percentage of rewards available for Innovators is determinable from the [latest config](https://api.tig.foundation/v1/getBlock).

2. Rewards earned by Innovators in Rounds 1 to 13 inclusive will be transferable by the Innovator at the end of Round 13 and rewards earned in each subsequent Round, will be transferable within five (5) working days after the end of each such Round.

3. Newly submitted algorithms are kept private for a certain number of Rounds before being pushed to a branch in the challenges repository. An algorithm submitted in round n will be pushed to the challenges repository at the start of round n+x, where x is determinable from the [latest config](https://api.tig.foundation/v1/getBlock).

4. Algorithms receive a single "merge point" for each block in which they exceed a certain threshold of "adoption" by Benchmarkers, where the threshold is determinable from the [latest config](https://api.tig.foundation/v1/getBlock).

5. At the end of a Round, for each challenge, an algorithm which has exceeded a certain number of merge points will be merged to the main branch of the challenges repository, where the relevant nuber of merge points is determinable from the [latest config](https://api.tig.foundation/v1/getBlock).

6. If there are multiple eligible algorithms, the one with the most merge points is selected.

7. Algorithms are more likely to be merged the greater their performance improvement.

8. The percentage of rewards available for Innovators is divided amongst challenges before being rewarded to merged algorithms pro-rata with their level of adoption by Benchmarkers.

9. If for a given challenge, the total adoption by Benchmarkers of merged algorithms is under a certain threshold, all Innovator rewards for that challenge will be divided evenly between the Innovator rewards for other challenges. The threshold is determinable from the [latest config](https://api.tig.foundation/v1/getBlock).