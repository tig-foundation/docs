# TIG Game Rules

Version 1.0, November 2023

## Table of Contents
1. [Rules for Innovators](#1-rules-for-innovators)
2. [Rules for Benchmarkers](#2-accelerating-science)

## 1. Rules for Innovators

Before playing TIG as a Innovator you must read and accept the [Terms and Conditions](TIG_Terms_And_Conditions.pdf).

The [challenges repository](https://github.com/tig-foundation/challenges) contains code for the challenges and algorithms for solving the challenges featured in The Innovation Game. Innovators are encouraged to build upon the work of previous Innovators in order to find more efficient algorithms for solving instances of the challenges in TIG. Innovators can use any of TIGs IP for this purpose under the terms of the TIG Outbound Game License (TOGL). Innovators may use other peoples algorithms as a basis, but must make a meaningful improvement before submitting any modified algorithm to TIG.

Innovators in The Innovation Game are players who submit algorithms for Benchmarkers to use. To be able to operate in a decentralized open R&D setting, The Innovation Game implements a novel system designed to:

* Disincentivise algorithm submissions that contribute minimal performance improvement
* Give new algorithm submissions time to be publicly scrutinized, before they are potentially iterated/improved upon
* Select for the best performing algorithms to merge to the main branch of the challenges repository

## 1.1. How it works

1. A fee has to be burned to submit an algorithm to The Innovation Game

    * Set to 0.005 ETH at time of writing (`<algorithm_submissions.eth_submission_fee>`*)

2. Newly submitted algorithms are kept private for a certain number of rounds before being pushed to a branch in the challenges repository (1 round is 1 week)

    * An algorithm submitted in round n will be pushed to the challenges repository at the start of round n+3 (`<algorithm_submissions.push_delay>`*)

3. Algorithms which have been pushed to the challenges repository can be used by Benchmarkers to solve instances of challenges, but are not yet eligible to earn TIG token rewards for their Innovators

    * Benchmarkers have to generate and submit a runtime signature along with any solutions found

    * The Innovation Game uses the runtime signature to ensure submitted solutions are attributed to the correct algorithm

4. Algorithms receive a single "merge point" every block in which they exceed a certain threshold of "adoption" by Benchmarkers

    * Threshold set to 25% at time of writing (`<algorithm_submissions.adoption_threshold_to_point>`*)

    * Benchmarkers are incentivised to find and use the algorithm with the best performance as they are rewarded based on the number of qualifying solutions they submit

    * Adoption is not just based on percentage of qualifying solutions found by an algorithm, but also takes into account penalties applied to the Benchmarker (see below for more on penalties)

5. At the end of a round, for each challenge an algorithm which has exceeded a certain number of merge points will get merged to the main branch of the challenges repository

    * If there are multiple eligible algorithms, the one with the most merge points is selected

    * Algorithms are more likely to be merged the greater their appreciable performance improvement

    * Set to 5,040 points (~3.5 days of blocks) at time of writing (`<algorithm_submissions.min_points_to_merge>`*)

6. Every block, a certain percentage of TIG tokens are divided amongst challenges before being rewarded to merged algorithms pro-rata with their adoption

    * In this way, Benchmarkers help to select for the best algorithms that they deem should be rewarded

    * Regards for Innovators are set to 15% of the total rewards at time of writing. All earned rewards will be transferable 90 days after TIG launches. Thereafter rewards will be transferable after the end of each round (weekly). (`<rewards.distribution.innovators>`*)

***Notes:**

* See the [latest parameter settings here](https://api.tig.foundation/v1/getBlock)

### 1.2. Repo Structure
The challenges repository stores challenges and algorithms in the following structure:
```
├── <challenge_id> # challenge folder with prefix cXXX_ (e.g. c001_satisfiability)
│   ├── __init__.py
│   ├── challenge.py
│   ├── README.md # challenge specific descriptions
│   ├── algorithms
│   │   ├── default.py # every challenge has a naive algorithm called default
│   │   ├── <algorithm_id>.py # user submitted algorithm
│   │   ├── ...
│   │   ├── <algorithm_id>.py # user submitted algorithm
│
├── <challenge_id> # another challenge
├── ...
└── <challenge_id> # another challenge
```

### 1.2.1 challenge.py
Every challenge.py implements a Difficulty and Challenge dataclass:
```
@dataclass
class Difficulty:
    <parameter1>: int
    <parameter2>: int

@dataclass
class Challenge:
    seed: int # The seed used to randomly generate this `Challenge` instance
    difficulty: Difficulty # An instance of `Difficulty` used to generate this `Challenge` instance.
    
    <other challenge specific fields>

    def verifySolution(solution) -> bool:
      # function that outputs true if solution solves the challenge instance. false otherwise

    @classmethod
    def generateInstance(seed: int, difficulty: Difficulty) -> "Challenge":
      # function that generates an instance of the challenge
```

These classes can be imported as follows:
```
from <challenge_id>.challenge import Challenge, Difficulty
```

### 1.2.2 algorithms

* An algorithm is a single python file that must define a `solveChallenge(challenge) -> list` function

* If you are using numpy, remember to convert numpy arrays to a python list. e.g. `arr.tolist()`

* Some challenge instances may not have a solution! Your algorithm should take this into account and try to exit early

* Your algorithm will run in a sandbox with no internet access and limited to built-in python 3.11.3 libraries, numpy 1.25.2, and the relevant challenge.py

* Solutions submitted by Benchmarkers are random sampled and verified by AWS lambdas. If your algorithm takes more than 1024MB of memory or takes longer than 5s to finish, the solution is rejected.

## 1.3. Our Challenges

If you want to propose a challenge to be featured in The Innovation Game, please reach out to us on Discord or at contact@tig.foundation

The Innovation Game currently features the following challenges:

* Boolean Satisfiability (3-SAT)

* Capacitated Vehicle Routing

* Knapsack Problem

## 1.4. Developing & Testing Your Algorithm

For newcomers, it is recommended that you start by copying, renaming and modifying an existing algorithm

To test your algorithm locally, you can run our provided script which has a small suite of default difficulty parameter combinations:

* Example: `python algo_tester.py c001_satisfiability default`

* Options: `python algo_tester.py --help`

## 1.5. Submitting Your Algorithm

Follow the steps on our [Innovator Dashboard](https://tig.foundation/innovator-dashboard) to:

* Upload your algorithm for testing

* Burn an amount of ETH as a submission fee

* Submit your algorithm


## 2. Rules for Benchmarkers
 
Before playing TIG as a Benchmarker you must read and accept the [Terms and Conditions](TIG_Terms_And_Conditions.pdf).

The [browser benchmarker repository](https://github.com/tig-foundation/browser-benchmarker) contains code to run a benchmarker for The Innovation Game in a browser with pyodide version 0.24.1.

Benchmarkers in The Innovation Game are players who select & use algorithms to solve instances of pseudo-randomly generated challenges. Benchmarkers can use any of TIGs algorithms for this purpose under the terms of the TIG Outbound Game License (TOGL).

Towards the goal of accelerating science, The Innovation Game implements a novel system designed so that Benchmarkers provide a market signal indicating which algorithms submissions will bring performance improvements for solving real scientific problems:

* Benchmarkers are rewarded based on the number of qualifying solutions they submit, helping to assess the performance of algorithms. 

### 2.1. Efficiently Verifiable Computation

* Instances of challenges are deterministically pseudo-randomly generated from a seed

* Benchmarkers can calculate as many seeds as they want, but cannot predict them. Calculated by hashing a nonce + following seed parameters:

    * block.random_hash (approximately timestamps when benchmark was started)

    * challenge_id (prevents re-use of same seed for different challenge)

    * algorithm_id (prevents re-use of same seed for different algorithm)

    * difficulty (prevents re-use of same seed for different difficulty)

    * player_id (prevents re-use of same seed for different player)

* A runtime_signature is automatically generated as an algorithm is run

    * Allows The Innovation Game to verify the algorithm a Benchmarker has used

* Benchmarkers batch solutions that share the same seed parameters into a "benchmark". 

* Benchmarkers submit benchmarks in 2 steps, a claim followed by proofs:

    * A claim only includes seed parameters along with the nonces for which the Benchmarker "claims" they have found solutions

    * After a claim is confirmed into a block, a certain number of nonces (`<verification.num_samples>`*) are randomly sampled

    * Benchmarker submits the data corresponding to the randomly sampled nonces as proofs

        * Proofs may be subject to verification by The Innovation Game using AWS lambda

        * If verification of a proof takes more than 1024MB of memory or takes longer than 5s to finish, it is is rejected

    * To have a reliable estimate of compute times, The Innovation Game strongly incentivise timely submissions, a delay period is calculated by multiplying a certain factor (`<verification.period_multiplier>`*) with the number of blocks between when a benchmark was started, and when proofs were submitted.

        * Only after the delay period will a benchmark become active

        * A benchmark's active period can be calculated by minusing its lifespan (`<qualifiers.lifespan>`*) from the number of blocks between when the benchmark was started, and when it became active

### 2.2. Difficulty Adjustment

* The Innovation Game uses a qualifier mechanism to incentivise Benchmarkers to increase the difficulty of their benchmarks

    * For each challenge, active benchmarks are sorted by difficulty before iteratively flagging the hardest difficulty benchmarks as qualifiers. The loop is stopped when there are no more benchmarks or a cutoff number of qualifying solutions has been exceeded (`<qualifiers.num_cutoff>)

    * The benchmarks on the Pareto frontier represents the "hardest" difficulty. For technical details of the frontiers mechanism see the [tigpy repository](https://github.com/tig-foundation/tigpy)

    * **Only benchmarks flagged as qualifiers are rewarded TIG tokens**

* The Innovation Game uses a preimage threshold mechanism to adjust the rate of solutions

    * A solution_id is generated by hashing a solution, its nonce, and its runtime signature

    * The solution_id must be below a certain number (preimage threshold) to be accepted as a solution. Each challenge has its own preimage threshold (`<challenges[x].preimage_threshold>`*)

    * The preimage threshold is increased/decreased based on the number of solutions submitted in past blocks compared with the target rate (`<preimage_threshold.target_solution_rate>`*)

### 2.3. Rewards for Benchmarkers

Rewards for Innovators are set to 85% of total rewards at the time of writing. All earned rewards will be transferable 90 days after TIG launches, and subsequent rewards will be transferable at the end of each round thereafter (weekly).

* The Innovation Game rewards Benchmarkers based on their average percentage of qualifiers across challenges

* If a Benchmarker has different percentage of qualifiers across challenges, a penalty is applied:

    * An imbalance penalty based on standard deviation of a Benchmarker's percentage of qualifiers across challenges is applied

    * Benchmarkers are incentivised to find about the same number of qualifying solutions for each challenge (the browser benchmarker does this automatically)

***Notes:**

* See the [latest parameter settings here](https://api.tig.foundation/v1/getBlock)