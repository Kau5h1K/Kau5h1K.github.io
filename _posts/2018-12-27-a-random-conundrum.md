---
layout: post
title: "A Random Conundrum"
date: 2018-12-27
tags: [probability, python, random numbers, hackers statistics]
excerpt: "With hacker statistics, calculate your chances of winning a bet using random number generators, loops and matplotlib"
comments: true
---
A while back, I have come across a coding challenge prompting to make a guess in terms of probability of a peculiar problem that caught my interest. *Imagine you're walking up a random sky scraper along with your friends and paused to play a game of dice to kill some time.*

- If the dice rolls **one** or **two**, you go one step **down**.

- If it rolls **three**, **four** or **five**, then you go one step **up**.

- If it lands a **six**, you reroll the dice and move the resulting number **upward**, without adding six to the sum.

- Since the steps start from zero, you obviously are denied going beneath that level.

- And with all good things arrive a catch which so happens to be your own clumsiness that may lead you to tumble down the stairs to square one, where you started (Step 0). It comes with a chance of **0.1%** with each move.

So, as ambitious as you are, you bet your friend that you would reach **60** steps or higher by throwing your dice a hundred times.
And with that, *our task is to calculate your chances of winning the petty bet.*
Although there are many ways to tackle this problem analytically, I would always lean towards using a *simulated* approach, one that would mimic and run the process a lot of times to determine the fraction of simulations wherein you win the bet and thereby, computing the mean probability of your winnings thereof. This is a form of *Hacker statistics*.

As one might guess, we need *random generators* to simulate the dice toss. Randomness has many uses in science, art, statistics, cryptography, gaming, gambling, and other fields.
All the functionality we need is contained in the *random package*, a sub-package of `numpy`. I'll be using two functions from this package:

- `seed()`
- `randint()`

We import the `numpy` package and set the seed as it offers reproducibility.
```python
import numpy as np
np.random.seed(123)
```
Then we use `randint()` to generate integers randomly.
```python
print(np.random.randint(1,7))
```
This simulates a dice.

Obeying the conditions of the bet, your next move depends on the number of eyes you throw with the dice. The conditional argument can be implemented with a simple `if-elif-else construct!`
```python
if dice <= 2 :
    step = step - 1
elif dice in [3,4,5] :
    step = step + 1
else :
    step = step + np.random.randint(1,7)
```
Proceeding further, we exploit a jargon called *random walk*. Interesting!
If a dice is used to determine the next step, it's said to be a random step. Similarly, if the dice is thrown a hundred times, we would have a succession of random steps, a random walk. This notion is observed across several disciplines. For instance, In science, the path traced by a molecule in a liquid or gas can be modeled as a random walk. Financial status of a gambler can also be considered as such.

We then initialize the random walk and store the random step count in it for each of the hundred times the dice is thrown.
```python
# Initialize random_walk
random_walk = [0]

# Simulate dice thrown a hundred times
for x in range(100) :
    # Set step: last element in random_walk
    step = random_walk[-1]

    # Roll the dice
    dice = np.random.randint(1,7)

    # Determine next step
    if dice <= 2:
        step = step - 1
    elif dice <= 5:
        step = step + 1
    else:
        step = step + np.random.randint(1,7)

    # append next_step to random_walk
    random_walk.append(step)

# Print random_walk
print(random_walk)
```
Wait! Here comes a possible error that might have slipped into the code unnoticed. If you run the code, you are sure to run into a result wherein there might be a negative step count.
Great! How do we resolve this?
Of course, we use a simple trick of disallowing the lower limit of `step` variable to take values below zero.
We take the first `If` construct and change its content appropriately as follows:
```python
step = max(0,step - 1)
```
Whew! We have indeed got some work done. What's a better time than now to visualize it and see how things stand.
Remember how you could use `matplotlib` to build a line plot? Well, time to put it to use.
```python
import matplotlib.pyplot as plt
plt.plot(random_walk)
plt.show()
```
![alt text](https://i.imgur.com/cr9uJG0.png "Figure 1")
This is pretty cool! You can clearly see how your random walk progressed.

A single random walk is one thing, but that doesn't tell you if you have a good chance at winning the bet. To get an idea about how big your chances are of reaching **60** steps, you can repeatedly simulate the random walk and collect the results.
We fill in the specification of the `for` loop so that the random walk is simulated **10** times. After the `random_walk` array is entirely populated, append the array to the `all_walks` list.

`all_walks` is a list of lists: every sub-list represents a single random walk. If you convert this list of lists to a `numpy` array, you can start making interesting plots!
We use `np.array()` to convert `all_walks` to a `numpy` array, `np_aw` and use `plt.plot()` on it.
We also transpose `np_aw` by calling `np.transpose()` on `np_aw` and plot it. Now every row in `np_all_walks` represents the position after **1** throw for the **10** random walks.
![alt text](https://i.imgur.com/7vWdp3c.png "Figure 2")
![alt text](https://i.imgur.com/uiVYoEI.png "Figure 3")
You can clearly see how the different simulations of the random walk went. Transposing the 2D `numpy` array was crucial; otherwise Python misunderstood.
Well then, It is crucial that we don't miss out on the last condition. Your very own *clumsiness*! ~~Damn you!~~

As you might recall, you tend to be a bit clumsy sometimes and has a **0.1%** chance of falling down to **step 0**. That calls for another random number generation. Basically, we can generate a random float between 0 and 1. If this value is less than or equal to **0.001**, we reset step to 0 as the snippet below and also simulate the random walk **250** times now:
```python
if np.random.rand() <=0.001 :
            step = 0
```
![alt text](https://i.imgur.com/yn7JUTw.png "Figure 4")

 Look at the plot. In some of the 250 simulations you're indeed taking a deep dive down!
 All these fancy visualizations have put us on a sidetrack. We still have to solve the *million-dollar problem*: What are the odds that you'll reach 60 steps high on the building?

 Basically, you want to know about the end points of all the random walks you've simulated. These end points have a certain distribution that you can visualize with a histogram. To make sure we've got enough simulations, we go a bit overboard. we simulate the random walk **500** times. We then use `plt.hist()` to build a histogram of endpoints of all the random walks wherever they ended up as the step count.
 *The entire code snippet is as follows:*
 ```python
 # import numpy and matplotlib , set seed
import numpy as np
import matplotlib.pyplot as plt
np.random.seed(123)
# Simulate random walk 500 times
all_walks = []
for i in range(500) :
    random_walk = [0]
    for x in range(100) :
        step = random_walk[-1]
        dice = np.random.randint(1,7)
        if dice <= 2:
            step = max(0, step - 1)
        elif dice <= 5:
            step = step + 1
        else:
            step = step + np.random.randint(1,7)
        if np.random.rand() <= 0.001 :
            step = 0
        random_walk.append(step)
    all_walks.append(random_walk)

# Create and plot np_aw_t
np_aw_t = np.transpose(np.array(all_walks))

# Select last row from np_aw_t: ends
ends = np_aw_t[-1]

# Plot histogram of ends, display plot
plt.hist(ends)
plt.show()
```
![alt text](https://i.imgur.com/myPwLsQ.png "Figure 5")

The histogram was created from a `numpy` array ends, that contains **500** integers. Each integer represents the end point of a random walk. To calculate the chance that this end point is greater than or equal to 60, we can count the number of integers in `ends` list that are greater than or equal to 60 and divide that number by 500, the total number of simulations.

Well then, what's the estimated chance that you'll reach **60** steps high if you play this game? The `ends` array is everything we need;
we use `numpy` package to fetch `mean` function to calculate the probability as follows:
```python
np.mean(end>=60)
```
And the result we get is **78.4%.**
So **YES**, looks like the claims made by you has come to fruition and you are definitely more likely to win your bet.

Hence, our random Conundrum is solved.
{: .notice}
