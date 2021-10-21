# Introduction
## Space Cows Introduction
A colony of Aucks (super-intelligent alien bioengineers) has landed on Earth and has created new species of farm animals! The Aucks are performing their experiments on Earth, and plan on transporting the mutant animals back to their home planet of Aurock. In this problem set, you will implement algorithms to figure out how the aliens should shuttle their experimental animals back across space.

## Getting started!
Download [ps1.py](https://raw.githubusercontent.com/lcsm29/edx-mit-6.00.2x/8120e6eaa5d96c762300e56a6764d99216d78a53/ps1/ps1.py), [ps1_cow_data.txt](https://raw.githubusercontent.com/lcsm29/edx-mit-6.00.2x/8120e6eaa5d96c762300e56a6764d99216d78a53/ps1/ps1_cow_data.txt), [ps1_partition.py](https://raw.githubusercontent.com/lcsm29/edx-mit-6.00.2x/8120e6eaa5d96c762300e56a6764d99216d78a53/ps1/ps1_partition.py).

Please do not rename the files we provide you with, change any of the provided helper functions, change function/method names, or delete provided docstrings. You will need to keep ps1_partition.py and ps1_cow_data.txt in the same folder as ps1.py.

## Transporting Cows Across Space!
The aliens have succeeded in breeding cows that jump over the moon! Now they want to take home their mutant cows. The aliens want to take all chosen cows back, but their spaceship has a weight limit and they want to minimize the number of trips they have to take across the universe. Somehow, the aliens have developed breeding technology to make cows with only integer weights.

The data for the cows to be transported is stored in `ps1_cow_data.txt`. All of your code for Part A should go into `ps1.py`.

First we need to load the cow data from the data file `ps1_cow_data.txt`, this has already been done for you and should let you begin working on the rest of this problem. If you are having issues getting the `ps1_cow_data.txt` to load, be sure that you have it in the same folder as the `ps1.py` that you are running.

You can expect the data to be formatted in pairs of x,y on each line, where x is the name of the cow and y is a number indicating how much the cow weighs in tons, and that all of the cows have unique names. Here are the first few lines of `ps1_cow_data.txt`:

Maggie,3
Herman,7
Betsy,9
...
<br>
<br>
<br>

# Part 1: Greedy Cow Transport
20.0/20.0 points (graded)

One way of transporting cows is to always pick the **heaviest cow that will fit** onto the spaceship first. This is an example of a greedy algorithm. So if there are only 2 tons of free space on your spaceship, with one cow that's 3 tons and another that's 1 ton, the 1 ton cow will get put onto the spaceship.

Implement a greedy algorithm for transporting the cows back across space in the function `greedy_cow_transport`. The function returns a list of lists, where each inner list represents a trip and contains the names of cows taken on that trip.

Note: Make sure not to mutate the dictionary of cows that is passed in!

## Assumptions:
* The order of the list of trips does not matter. That is, [[1,2],[3,4]] and [[3,4],[1,2]] are considered equivalent lists of trips.
* All the cows are between 0 and 100 tons in weight.
* All the cows have unique names.
* If multiple cows weigh the same amount, break ties arbitrarily.
* The spaceship has a cargo weight limit (in tons), which is passed into the function as a parameter.

## Example:
Suppose the spaceship has a weight limit of 10 tons and the set of cows to transport is `{"Jesse": 6, "Maybel": 3, "Callie": 2, "Maggie": 5}`.

The greedy algorithm will first pick `Jesse` as the heaviest cow for the first trip. There is still space for 4 tons on the trip. Since `Maggie` will not fit on this trip, the greedy algorithm picks `Maybel`, the heaviest cow that will still fit. Now there is only 1 ton of space left, and none of the cows can fit in that space, so the first trip is `[Jesse, Maybel]`.

For the second trip, the greedy algorithm first picks `Maggie` as the heaviest remaining cow, and then picks `Callie` as the last cow. Since they will both fit, this makes the second trip `[Maggie, Callie]`.

The final result then is `[["Jesse", "Maybel"], ["Maggie", "Callie"]]`.
<br>
<br>
<br>

# Part 2: Brute Force Cow Transport
20.0/20.0 points (graded)

Another way to transport the cows is to look at **every possible combination of trips and pick the best one**. This is an example of a brute force algorithm.

Implement a brute force algorithm to find the **minimum number of trips** needed to take all the cows across the universe in the function `brute_force_cow_transport`. The function returns a list of lists, where each inner list represents a trip and contains the names of cows taken on that trip.

## Notes:
* Make sure not to mutate the dictionary of cows!
* In order to enumerate all possible combinations of trips, you will want to work with set partitions. We have provided you with a helper function called get_partitions that generates all the set partitions for a set of cows. More details on this function are provided below.

## Assumptions:
* Assume that order doesn't matter. (1) `[[1, 2], [3, 4]]` and `[[3, 4], [1, 2]]` are considered equivalent lists of trips. (2) `[[1, 2], [3, 4]]` and `[[2, 1], [3, 4]]` are considered the same partitions of `[1, 2, 3, 4]`.
* You can assume that all the cows are between 0 and 100 tons in weight.
* All the cows have unique names.
* If multiple cows weigh the same amount, break ties arbitrarily.
* The spaceship has a cargo weight limit (in tons), which is passed into the function as a parameter.

## Helper function `get_partitions` in `ps1_partitions.py`:
To generate all the possibilities for the brute force method, you will want to work with set partitions.

For instance, all the possible 2-partitions of the list `[1, 2, 3, 4]` are `[[1, 2] ,[3, 4]]`, `[[1, 3], [2, 4]]`, `[[2, 3], [1, 4]]`, `[[1], [2, 3, 4]]`, `[[2], [1, 3, 4]]`, `[[3], [1, 2, 4]]`, `[[4], [1, 2, 3]]`.

To help you with creating partitions, we have included a helper function get_partitions(L) that takes as input a list and returns a generator that contains all the possible partitions of this list, from 0-partitions to n-partitions, where n is the length of this list.

You can review more on generators in the Lecture 2 Exercise 1. To use generators, you must iterate over the generator to retrieve the elements; you cannot index into a generator! For instance, the recommended way to call get_partitions on a list [1,2,3] is the following. Try it out in `ps1_partitions.py` to see what is printed!
```py:
for partition in get_partitions([1,2,3]):
    print(partition)
```

## Example:
Suppose the spaceship has a cargo weight limit of 10 tons and the set of cows to transport is `{"Jesse": 6, "Maybel": 3, "Callie": 2, "Maggie": 5}`.

The brute force algorithm will first try to fit them on only one trip, `["Jesse", "Maybel", "Callie", "Maggie"]`. Since this trip contains 16 tons of cows, it is over the weight limit and does not work. Then the algorithm will try fitting them on all combinations of two trips. Suppose it first tries `[["Jesse", "Maggie"], ["Maybel", "Callie"]]`. This solution will be rejected because Jesse and Maggie together are over the weight limit and cannot be on the same trip. The algorithm will continue trying two trip partitions until it finds one that works, such as `[["Jesse", "Callie"], ["Maybel", "Maggie"]]`.

The final result is then `[["Jesse", "Callie"], ["Maybel", "Maggie"]]`. Note that depending on which cow it tries first, the algorithm may find a different, optimal solution. Another optimal result could be `[["Jesse", "Maybel"],["Callie", "Maggie"]]`.
<br>
<br>
<br>

# Part 3: Comparing the Cow Transport Algorithms
## POLL: ALGORITHM INTUITION
(ungraded) Before doing the task in this part, answer the following question to see your intuition for how the greedy and brute force algorithm run. In terms of time, which algorithm do you expect will run faster?

## RESULTS
* Greedy Algorithm: 94%
* Brute Force Algorithm: 6%
- Results gathered from 100 respondents.

Implement `compare_cow_transport_algorithms`. Load the cow data in `ps1_cow_data.txt`, and then run your greedy and brute force cow transport algorithms on the data to find the minimum number of trips found by each algorithm and how long each method takes. Use the default weight limits of 10 for both algorithms. Make sure you’ve tested both your greedy and brute force algorithms before you implement this!

## Hints:
* You can measure the time a block of code takes to execute using the `time.time()` function as follows. This prints the duration in seconds, as a float. For a very small fraction of a second, it will print 0.0.
```py:
start = time.time()
## code to be timed
end = time.time()
print(end - start)
```
* Using the given default weight limits of 10 and the given cow data, both algorithms should not take more than a few seconds to run.

## Part 3-1
2.0/2.0 points (graded)

Now that you have run your benchmarks, which algorithm runs faster?

- [x] The Greedy Transport Algorithm
- [ ] The Brute Force Transport Algorithm
- [ ] They take the same amount of time

## Part 3-2
2.0/2.0 points (graded)

Consider the properties of the GREEDY algorithm. Will it return the optimal solution?

- [ ] Yes, all the time
- [ ] No, never
- [x] It could, but it depends on the data, not always.

## Part 3-3
2.0/2.0 points (graded)

Consider the properties of the BRUTE FORCE algorithm. Will it return the optimal solution?

- [x] Yes, all the time
- [ ] No, never
- [ ] It could, but it depends on the data, not always.
