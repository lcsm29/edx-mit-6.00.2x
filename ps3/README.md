# Part A: probabilities
8.0/8.0 points (graded)

This part of the problem set involves some pencil-and-paper exercises. It will help you practice and understand simple probability and statistics.

Let's say Alvin will catch the flu with probability of 1/10 during any given month. Let's also assume that Alvin can catch the flu only once per month, and that if he has caught the flu, the flu virus will die by the end of the month. What is the probability of the following events?

Answer each question in reduced fraction form - eg `1/5` instead of `2/10`.

1. He catches the flu in September, October and November. `1/1000`
2. He catches the flu in September and then again in November, but not in October. `9/1000`
3. He catches the flu exactly once in the three months from September through November. `243/1000`
4. He catches the flu in two or more of the three months from September through November. `7/250`
<br>
<br>
<br>

# Part B: Introduction
## Introduction
In this problem set, using Python and Pylab, you will design and implement a stochastic simulation of patient and virus population dynamics, and reach conclusions about treatment regimens based on the simulation results.

## Getting Started
Download files you need:
* [ps3.py](https://raw.githubusercontent.com/lcsm29/edx-mit-6.00.2x/ef0572530b86683ba737def2b1e7db037d129d5b/ps3/ps3b.py), a skeleton file for Part B.
* [`ps3b_precompiled_3X.pyc`](https://github.com/lcsm29/edx-mit-6.00.2x/tree/ef0572530b86683ba737def2b1e7db037d129d5b/ps3), precompiled module for Python 3.X that assists with the visualization code.
<br>
<br>
<br>

# Part B: Background
## Background: Viruses, Drug Treatments, and Computational Models
Viruses such as HIV and H1N1 represent a significant challenge to modern medicine. One of the reasons that they are so difficult to treat is their ability to evolve.

As you may know from introductory biology classes, the traits of an organism are determined by its genetic code. When organisms reproduce, their offspring will inherit genetic information from their parent. This genetic information will be modified, either because of mixing of the two parents' genetic information, or through mutations in the genome replication process, thus introducing diversity into a population.

Viruses are no exception. Two characteristics of viruses make them particularly difficult to treat. The first is that their replication mechanism often lacks the error checking mechanisms that are present in more complex organisms. This speeds up the rate of mutation. Secondly, viruses replicate extremely quickly (orders of magnitude faster than humans) -- thus, while we may be used to thinking of evolution as a process which occurs over long time scales, populations of viruses can undergo substantial evolutionary changes within a single patient over the course of treatment.

These two characteristics allow a virus population to acquire genetic resistance to therapy quickly. In this problem set, we will make use of simulations to explore the effect of introducing drugs on the virus population and determine how best to address these treatment challenges within a simplified model.

Computational modeling has played an important role in the study of viruses such as HIV (for example, see this paper, by MIT graduate David Ho). In this problem, we will implement a highly simplified stochastic model of virus population dynamics. Many details have been swept under the rug (host cells are not explicitly modeled and the size of the population is several orders of magnitude less than the size of actual virus populations). Nevertheless, our model exhibits biologically relevant characteristics and will give you a chance to analyze and interpret interesting simulation data.

## Spread of a Virus in a Person
In reality, diseases are caused by viruses and have to be treated with medicine, so in the remainder of this problem set, we'll be looking at a detailed simulation of the spread of a virus within a person. We've provided you with skeleton code in [**ps3b.py**](./ps3b.py).
<br>
<br>
<br>

# Part B: Problem 1
## Part B: Problem 1: Implementing a Simple Simulation (No Drug Treatment)
15.0/15.0 points (graded)

We start with a trivial model of the virus population - the patient does not take any drugs and the viruses do not acquire resistance to drugs. We simply model the virus population inside a patient as if it were left untreated.

## SimpleVirus class
To implement this model, you will need to fill in the `SimpleVirus` class, which maintains the state of a single virus particle. You will implement the methods `__init__`, `getMaxBirthProb`, `getClearProb`, `doesClear`, and reproduce according to the specifications. Use `random.random()` for generating random numbers to ensure that your results are consistent with ours.

<details>
<summary>Hint: random seed</summary>
<br>

During debugging, you might want to use `random.seed(0)` so that your results are reproducible.
</details>

The `reproduce` method in `SimpleVirus` should produce an offspring by returning a new instance of `SimpleVirus` with probability: `self.maxBirthProb * (1 - popDensity)`. This method raises a `NoChildException` if the virus particle does not reproduce. For a reminder on raising execptions, review the [Python docs](http://docs.python.org/3/tutorial/errors.html#raising-exceptions).

`self.maxBirthProb` is the birth rate under optimal conditions (the virus population is negligible relative to the available host cells so there is ample nourishment available). `popDensity` is defined as the ratio of the current virus population to the maximum virus population for a patient and should be calculated in the `update` method of the `Patient` class.

## Patient class
You will also need to implement the `Patient` class, which maintains the state of a virus population associated with a patient.

The `update` method in the `Patient` class is the inner loop of the simulation. It modifies the state of the virus population for a single time step and returns the total virus population at the end of the time step. At every time step of the simulation, each virus particle has a fixed probability of being cleared (eliminated from the patient's body). If the virus particle is not cleared, it is considered for reproduction. If you utilize the population density correctly, you shouldn't need to provide an explicit check that the virus population exceeds maxPop when you are calculating how many offspring are added to the population -- you just calculate the new population density and use that for the next call to `update`.

Unlike the clearance probability, which is constant, the probability of a virus particle reproducing is a function of the virus population. With a larger virus population, there are fewer resources in the patient's body to facilitate reproduction, and the probability of reproduction will be lower. One way to think of this limitation is to consider that virus particles need to make use of a patient's cells to reproduce; they cannot reproduce on their own. As the virus population increases, there will be fewer available host cells for viruses to utilize for reproduction.

To summarize, `update` should first decide which virus particles are cleared and which survive by making use of the `doesClear` method of each `SimpleVirus` instance, then update the collection of `SimpleVirus` instances accordingly. With the surviving `SimpleVirus` instances, `update` should then call the `reproduce` method for each virus particle. Based on the population density of the surviving `SimpleVirus` instances, `reproduce` should either return a new instance of `SimpleVirus` representing the offspring of the virus particle, or raise a `NoChildException` indicating that the virus particle does not reproduce during the current time step. The `update` method should update the attributes of the patient appropriately under either of these conditions. After iterating through all the virus particles, the `update` method returns the number of virus particles in the patient at the end of the time step.

<details>
<summary>Hint: mutating objects</summary>
<br>

Be very wary about mutating an object while iterating over its elements. It is best to avoid this entirely (consider introducing additional "helper" variables). See the [6.00.2x Style Guide](https://courses.edx.org/assets/courseware/v1/3d1ce5279f58011523cc5ee77698684d/asset-v1:MITx+6.00.2x+3T2021+type@asset+block/6.002x_style_guide.pdf) for more information.
</details>

Note that the mapping between time steps and actual time will vary depending on the type of virus being considered, but for this problem set, think of a time step as a simulated hour of time.

**About the grader**: When defining a Patient class member variable to store the viruses list representing the virus population, please use the name self.viruses in order for your code to be compatible with the grader and to pass one of the tests.

**Note: If you want to use numpy arrays, you should add the following lines at the beginning of your code for the grader:**
```
import os
os.environ["OPENBLAS_NUM_THREADS"] = "1"
```
Then, do `import numpy as np` and use `np.METHOD_NAME` in your code.
<br>
<br>
<br>

# Part B: Problem 2
## Part B: Problem 2-1: Running and Analyzing a Simple Simulation (No Drug Treatment)
10.0/10.0 points (graded)

You should start by understanding the population dynamics before introducing any drug.

Fill in the function `simulationWithoutDrug(numViruses, maxPop, maxBirthProb, clearProb, numTrials)` that instantiates a `Patient`, simulates changes to the virus population for 300 time steps (i.e., 300 calls to `update`), and plots the average size of the virus population as a function of time; that is, the x-axis should correspond to the number of elapsed time steps, and the y-axis should correspond to the average size of the virus population in the patient. The population at time=0 is the population after the first call to `update`.

Run the simulation for `numTrials` trials, where `numTrials` in this case can be up to 100 trials. Use pylab to produce a plot (with a single curve) that displays the average result of running the simulation for many trials. Make sure you run enough trials so that the resulting plot does not change much in terms of shape and time steps taken for the average size of the virus population to become stable. **Don't forget to include axes labels, a legend for the curve, and a title on your plot**.

You should call `simulationWithoutDrug` with the following parameters:
* `numViruses` = 100
* `maxPop` (maximum sustainable virus population) = 1000
* `maxBirthProb` (maximum reproduction probability for a virus particle) = 0.1
* `clearProb` (maximum clearance probability for a virus particle) = 0.05

Thus, your simulation should be instantiatating one `Patient` with a list of 100 `SimpleVirus` instances. Each `SimpleVirus` instance in the viruses list should be initialized with the proper values for `maxBirthProb` and `clearProb`.

<details>
<summary>Hint: graphing</summary>
<br>

Your graph should contain one line that represents the average of many different trials. One way of computing the average involves holding all of your data in one list, with one element for each of 300 time steps, and adding to each data point during each trial. Then, at the end, each element of the list is divided by the total number of trials, thus taking the average of each element of the list. However, if this idea confuses you, feel free to ignore the hint and implement it however makes the most sense to you.
</details>

Consult [reference documentation on pylab](http://matplotlib.sourceforge.net/) as reference. Scroll down on the page to find a list of all the [plotting commands](http://matplotlib.org/api/pyplot_summary.html) in pylab.

<details>
<summary>Hint: testing your simulation</summary>
<br>

Compared to the previous problem sets, testing your simulation code is more challenging, because the behavior of the code is stochastic, and the expected output is not exactly known. How do you know whether your plots are correct or not? One way to test this is to run the simulation with extreme input values (i.e. initialization parameters), and check that the output matches your intuition. For example, if `maxBirthProb` is set to 0.99 instead of 0.1, then you would expect that the virus population rapidly increases over a short period of time. Similarly, if you run your simulation with `clearProb` = 0.99 and `maxBirthProb` = 0.1, then you should see the virus population quickly decreasing within a small number of steps. You can also try to vary the input values, and check whether the output plots change as you expect. For example, if you run multiple simuation runs, each time increasing `maxBirthProb`, the curves in the successive plots should show an "upward" trend, since the virus will reproduce faster with a higher `maxBirthProb`.
</details>

For further testing, we have provided the .pyc (compiled Python) files for the completed `Patient` and `SimpleVirus` classes (and for Problem 5, the `ResistantVirus` and `TreatedPatient` classes) that you can use to confirm that your code is generating the correct results during simulation.

If you comment out your versions of these classes in `ps3b.py`, and add the following import statements to the top of your file, you can run the simulation using our pre-compiled implementation of these classes to make sure you are obtaining the correct results. This is a good way to test if you've implemented these classes correctly. Make sure to comment out the import statement and uncomment your implementations before moving to Problem 4.

### Notes:
* **Note: If you want to use numpy arrays, you should add the following lines at the beginning of your code for the grader:**
```
import os
os.environ["OPENBLAS_NUM_THREADS"] = "1"
```
Then, do `import numpy as np` and use `np.METHOD_NAME` in your code.

* Use this code in the grader, to plot:
```
pylab.plot(YOUR_Y_AXIS_VALUES, label = "SimpleVirus")
pylab.title("SimpleVirus simulation")
pylab.xlabel("Time Steps")
pylab.ylabel("Average Virus Population")
pylab.legend(loc = "best")
pylab.show()
```

* For Python 3.5: `from ps3b_precompiled_35 import *`
* For Python 3.6: `from ps3b_precompiled_36 import *`
* For Python 3.7: `from ps3b_precompiled_37 import *`
* For Python 3.8: `from ps3b_precompiled_38 import *`
* For Python 3.9: `from ps3b_precompiled_39 import *`
* For Python 3.10: `from ps3b_precompiled_310 import *`

## Part B: Problem 2-2
5.0/5.0 points (graded)

A good question to consider as you look at your plot is: about how long does it take before the population stops growing?
- [ ] About 20 time-steps
- [ ] About 80 time-steps
- [x] About 150 time-steps
- [ ] About 200 time-steps
- [ ] About 250 time-steps
<br>
<br>
<br>

# Part B: Problem 3
## Part B: Problem 3: Implementing a Simulation With Drugs
10.0/10.0 points (graded)

In this problem, we consider the effects of both administering drugs to the patient and the ability of virus particle offsprings to inherit or mutate genetic traits that confer drug resistance. As the virus population reproduces, mutations will occur in the virus offspring, adding genetic diversity to the virus population. Some virus particles gain favorable mutations that confer resistance to drugs.

### ResistantVirus class
In order to model this effect, we introduce a subclass of `SimpleVirus` called `ResistantVirus`. `ResistantVirus` maintains the state of a virus particle's drug resistances, and accounts for the inheritance of drug resistance traits to offspring. Implement the `ResistantVirus` class.

<details>
<summary>Hint: `reproduce` function child resistances</summary>
<br>

If you are really unsure about how to think about what each child resistances should be changed to, here is a different approach. If the probability `mutProb` is successful, the child resistance switches. Otherwise, the child resistance stays the same as the parent resistance.
</details>

**Note: If you want to use numpy arrays, you should add the following lines at the beginning of your code for the grader:**
```
import os
os.environ["OPENBLAS_NUM_THREADS"] = "1"
```
Then, do `import numpy as np` and use `np.METHOD_NAME` in your code.
<br>
<br>
<br>

# Part B: Problem 4
## Part B: Problem 4: TreatedPatient Class
10.0/10.0 points (graded)

We also need a representation for a patient that accounts for the use of drug treatments and manages a collection of `ResistantVirus` instances. For this, we introduce the `TreatedPatient` class, which is a subclass of `Patient`. `TreatedPatient` must make use of the new methods in `ResistantVirus` and maintain the list of drugs that are administered to the patient.

Drugs are given to the patient using the TreatedPatient class's `addPrescription()` method. What happens when a drug is introduced? The drugs we consider **do not directly kill virus particles lacking resistance to the drug**, but prevent those virus particles from reproducing (much like actual drugs used to treat HIV). Virus particles with resistance to the drug continue to reproduce normally. Implement the `TreatedPatient` class.

<details>
<summary>Hint: reproduce function child resistances</summary>
<br>

If you are really unsure about how to think about what each child resistances should be changed to, here is a different approach. If the probability `mutProb` is successful, the child resistance switches. Otherwise, the child resistance stays the same as the parent resistance.
</details>

**Note: If you want to use numpy arrays, you should add the following lines at the beginning of your code for the grader:**
```
import os
os.environ["OPENBLAS_NUM_THREADS"] = "1"
```
Then, do `import numpy as np` and use `np.METHOD_NAME` in your code.
<br>
<br>
<br>

# Part B: Problem 5
## Part B: Problem 5: Running and Analyzing a Simulation With a Drug
10.0/10.0 points (graded)

In this problem, we will use the implementation you filled in for Problem 4 to run a simulation. You will create a `TreatedPatient` instance with the following parameters, then run the simulation:

* `viruses`, a list of 100 `ResistantVirus` instances
* `maxPop`, maximum sustainable virus population = 1000

Each `ResistantVirus` instance in the viruses list should be initialized with the following parameters:

* `maxBirthProb`, maximum reproduction probability for a virus particle = 0.1
* `clearProb`, maximum clearance probability for a virus particle = 0.05
* `resistances`, The virus's genetic resistance to drugs in the experiment = {'guttagonol': False}
* `mutProb`, probability of a mutation in a virus particle's offspring = 0.005

Run a simulation that consists of 150 time steps, followed by the addition of the drug, guttagonol, followed by another 150 time steps. You should make use of the function `simulationWithDrug(numViruses, maxPop, maxBirthProb, clearProb, resistances, mutProb, numTrials)`. As with problem 3, perform up to 100 trials and make sure that your results are repeatable and representative.

Create one plot that records both the average total virus population and the average population of guttagonol-resistant virus particles over time.

A few good questions to consider as you look at your plots are: What trends do you observe? Are the trends consistent with your intuition? Feel free to discuss the answers to these questions in the forum, to fully cement your understanding of this problem set, processing and interpreting data.

Again, as in Problem 2, you can use the provided .pyc file to check that your implementation of the `TreatedPatient` and `ResistantVirus` classes work as expected.

**Note: If you want to use numpy arrays, you should add the following lines at the beginning of your code for the grader:**
```
import os
os.environ["OPENBLAS_NUM_THREADS"] = "1"
```
Then, do `import numpy as np` and use `np.METHOD_NAME` in your code.
