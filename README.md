
# Introduction to Probability - Lab

## Introduction

Now that you know what sets are, we can go on and work with two sets that are of key importance when talking about probability: the event space and the sample space. These two concepts are foundational for calculating probabilities when assuming each event in the event space *has a same probability of happening*. Typical examples are rolling a dice (if the dice is "fair", the chance of throwing each number between 1 and 6 is 1/6) and flipping a coin (1/2 heads vs tails). You'll get a better sense of how all of this works in this lab.

## Objectives

You will be able to:

- Learn about experiments, outcomes and event space
- Understand probability through the law of relative frequency
- Learn about the probability axioms
- Learn about the addition law of probability
- Learn that where each outcome is equally likely, the probability is equal to number of outcomes in the event space divided by number of outcomes in the sample space


##  Sample space, event space and the law of relative frequency

#### a. Let's throw a dice once: formula of Laplace

First, create a set `roll_dice` that holds the sample space.


```python
roll_dice = set([1,2,3,4,5,6])
```

Now, let's assume that the event space is defined by "throwing a number higher than 4". This means that we consider the outcome "successful" if a 5 or a 6 is thrown. Create a set that holds these values.


```python
event = set([5,6])
```

Now use the formule $P(E) = \dfrac{|E|}{|S|}$ to calculate the probability.


```python
prob_5_6 = len(event) / len(roll_dice)
prob_5_6  # 0.3333333333333333
```




    0.3333333333333333



Using this formula, it should be clear that the answer is 1/3 or 0.3333....  

#### b. Now, let's simulate rolling dice to see how the law of relative frequency works.

As mentioned in the lecture, the law of relative frequency can be used to prove certain probabilities. But how does this work exactly? You're about to find out!

$$P(E) = \lim_{n\rightarrow\infty} \dfrac{S{(n)}}{n}$$

As you can see in the formula, the law states that when repeating an experiment $n$ times, where $n$ is very big, and you divide the number of "good" outcomes by the sample space (here we call it event E), you get to the probability of the event E. It should be clear that we get a more accurate number for P(E) when $n$ grows.

Let's see how this works. First, let's randomly generate values between 1 and 6. You can use `numpy` (imported as `np`) to generate random integers between 1 and 6 by setting the correct arguments. The `np.random` module is a very useful tool for this. We helped you with the code here, but you'll get more practice and a thorough explanation later on!


```python
import numpy as np
np.random.randint(1,7) # you will get a random value between 1 and 6. See how it changes when you rerun
```




    4



Now, let's repeat this expermient 10 times, then 1000 times, then 1 million times, then 100 million times. 
You can do this by specifying the argument `size` within the numpy function used above. Store the values in the pre-defined variables below.


```python
np.random.seed(12345) # to make sure there is no randomness

dice_10 = np.random.randint(1,7,size=10)
dice_1k = np.random.randint(1,7,size= 1000)
dice_1m = np.random.randint(1,7,size=1000000)
dice_100m = np.random.randint(1,7,size=100000000)
```


```python
dice_10
```




    array([3, 6, 6, 2, 5, 2, 6, 3, 6, 2])



next, let's count the number of "events". Remember that an event here is defined as throwing a 5 or a 6. Store them in the values below.


```python
#slow way
event_10 = len([x for x in dice_10 if x in [5,6]])
event_1k = len([x for x in dice_1k if x in [5,6]])
event_1m = len([x for x in dice_1m if x in [5,6]])
event_100m = len([x for x in dice_100m if x in [5,6]])
```


```python
#fast way
event_10 = np.sum(dice_10>4)
event_1k = np.sum(dice_1k>4)
event_1m = np.sum(dice_1m>4)
event_100m = np.sum(dice_100m>4)
```


```python
event_1k
```




    331



Next, you'll divide the number of events for each $n$ by the respective values for $n$. What do you see?


```python
prob_10 = event_10 / len(dice_10)
prob_1k = event_1k / len(dice_1k)
prob_1m = event_1m / len(dice_1m)
prob_100m = event_100m / len(dice_100m)
prob_10, prob_1k, prob_1m, prob_100m  # 0.5 0.331 0.333657 0.33329752
```




    (0.5, 0.331, 0.333657, 0.33329752)



You see that the probability converges to 0.3333333... for higher values of $n$. 

##  The Probability Axioms

You're working at the United Nations, and want to get a better sense of the world population. 

You come across some numbers and find the list of probabilities of being an inhabitant for each of the seven continents (rounded up to 3 digits):

- P(Africa) = 0.161
- P(Antarctica) = 0.000
- P(Asia) = 0.598
- P(Europe) = 0.10
- P(North-America) = 0.079
- P(Australia) = 0.005
- P(South-America) = 0.057

store these values using the variable names below:


```python
P_afr = 0.161
P_ant = 0.000
P_as = 0.598
P_eur = 0.10
P_na = 0.079
P_aus = 0.005
P_sa = 0.057
```

Now create the sample space set names `continents`. Store the sample space in a numpy array.


```python
continents = np.array([P_afr, P_ant, P_as, P_eur, P_na, P_aus, P_sa])
print(continents)
```

    [0.161 0.    0.598 0.1   0.079 0.005 0.057]


We want to make sure that the three probability axioms are fulfilled, because they assure us that $(\Omega,E,P)$ is a **probability space**:

- if we have a sample space $S$ (or $\Omega$)
- if we have an event space $E$ and a probability measure $P$, 
- **and** the three probability axioms are fulfilled, 

The third axiom is fairly ad hoc, and you will basically have to deduct from the context whether individual events are independent. It is fairly straightforward, however, that people can not be inhabitants of two continents at the same time, so for now, we will assume that we're good for axiom three.

However, we can use the numpy array `continents` to verify if axiom 1 and 2 are fulfilled. Create a function "axioms" that returns the message "We're good!" if both axiom 1 and 2 are fulfilled, and "Not quite!" if that's not the case.


```python
def check_axioms(sample_space):
    axiom_1a = (sample_space <=1).all()
    #print(axiom_1a)
    axiom_1b = (sample_space >=0).all()
    #print(axiom_1b)
    axiom_2 = np.sum(sample_space) == 1
    #print(axiom_2)
    if axiom_1a and axiom_1b and axiom_2:
        return "We're good!"
    else:
        return "Not quite"
```

Now test your newly created function out on `continents`


```python
check_axioms(continents)
```

    True
    True
    True





    "We're good!"



You want to make sure your test returns `"Not quite!"` for the following numpy arrays. Go ahead and test away!


```python
test_1 = np.array([0.05, 0.2, 0.3, 1.01])
test_2 = np.array([0.05, 0.5, 0.6, -0.15])
test_3 = np.array([0.043,0.05,.02,0.3,0.2])
```


```python
print(check_axioms(test_1))
print(check_axioms(test_2))
print(check_axioms(test_3))
```

    Not quite
    Not quite
    Not quite


Great! We tested it and seems like our set `continents` is a true probability space.

## Some more practice on the sample and event spaces

In this exercise, we'll look at possible outcomes when throwing a dice twice. For your convenience, we created the NumPy array below.

Next, we'll compute a couple or probabilities associated with doing this.


```python
import numpy as np
sample_dice = np.array([(1, 1), (1, 2), (1, 3), (1, 4), (1, 5), (1, 6),
              (2, 1), (2, 2), (2, 3), (2, 4), (2, 5), (2, 6),
              (3, 1), (3, 2), (3, 3), (3, 4), (3, 5), (3, 6),
              (4, 1), (4, 2), (4, 3), (4, 4), (4, 5), (4, 6),
              (5, 1), (5, 2), (5, 3), (5, 4), (5, 5), (5, 6),
              (6, 1), (6, 2), (6, 3), (6, 4), (6, 5), (6, 6)])
```

Look at the shape of the array to reassure we haven't made any mistakes.


```python
sample_dice.shape # should be equal to (36,2)
```




    (36, 2)



Use Python to obtain the following probabilities:

#### a. What is the probability of throwing a 5 at least once?

First, use sample_dice to get "True" values for each time a 5 occurs.


```python
set_5 = np.isin(sample_dice, 5)
set_5
```




    array([[False, False],
           [False, False],
           [False, False],
           [False, False],
           [False,  True],
           [False, False],
           [False, False],
           [False, False],
           [False, False],
           [False, False],
           [False,  True],
           [False, False],
           [False, False],
           [False, False],
           [False, False],
           [False, False],
           [False,  True],
           [False, False],
           [False, False],
           [False, False],
           [False, False],
           [False, False],
           [False,  True],
           [False, False],
           [ True, False],
           [ True, False],
           [ True, False],
           [ True, False],
           [ True,  True],
           [ True, False],
           [False, False],
           [False, False],
           [False, False],
           [False, False],
           [False,  True],
           [False, False]])



Next, make sure that you get a value `True` for each pair where at least one 5 was thrown.


```python
true_5 = np.any(set_5, axis = 1)
print(true_5)
```

    [False False False False  True False False False False False  True False
     False False False False  True False False False False False  True False
      True  True  True  True  True  True False False False False  True False]


Applying the `sum()` function you can get to the total number of items in the event space. Divide this by the total number in the sample space.


```python
prob_5 = np.sum(true_5) / len(sample_dice)
print(prob_5)
```

    0.3055555555555556


#### b. What is the probability of throwing a 5 or 6 at least once?


```python
set_5 = np.isin(sample_dice, 5)
set_6 = np.isin(sample_dice, 6)
```


```python
set_5_6 = set_5 + set_6
set_5_6
```




    array([[False, False],
           [False, False],
           [False, False],
           [False, False],
           [False,  True],
           [False,  True],
           [False, False],
           [False, False],
           [False, False],
           [False, False],
           [False,  True],
           [False,  True],
           [False, False],
           [False, False],
           [False, False],
           [False, False],
           [False,  True],
           [False,  True],
           [False, False],
           [False, False],
           [False, False],
           [False, False],
           [False,  True],
           [False,  True],
           [ True, False],
           [ True, False],
           [ True, False],
           [ True, False],
           [ True,  True],
           [ True,  True],
           [ True, False],
           [ True, False],
           [ True, False],
           [ True, False],
           [ True,  True],
           [ True,  True]])




```python
set_any_5_6 = np.any(set_5_6, axis=1)
print(set_any_5_6) 
```

    [False False False False  True  True False False False False  True  True
     False False False False  True  True False False False False  True  True
      True  True  True  True  True  True  True  True  True  True  True  True]



```python
prob_5_6 = np.sum(set_any_5_6) / len(sample_dice)
print(prob_5_6)
```

    0.5555555555555556


#### c. What is the probability of the outcome having a sum of exactly 8?


```python
sum_dice = np.sum(sample_dice, axis=1)
sum_8 = np.isin(sum_dice, 8)
sum_8
```




    array([False, False, False, False, False, False, False, False, False,
           False, False,  True, False, False, False, False,  True, False,
           False, False, False,  True, False, False, False, False,  True,
           False, False, False, False,  True, False, False, False, False])




```python
prob_sum_8 = np.sum(sum_8) / len(sample_dice)
print(prob_sum_8)
```

    0.1388888888888889



```python
len(sample_dice)
```




    36



## Now let's try creating your own event space!

A teaching assistant is holding office hours so students can make appointments. She has 6 appointments scheduled today, 3 by male students, and 3 by female students. 

Create a NumPy array to list the event space, (all the possible permutations of these appointments) the same way as we did this for you in the "throwing a dice twice" exercise. It will be quite a bit of typing, as your resulting NumPy array will have a shape (20,6)!


```python
sample_mf= np.array([['M', 'M', 'M', 'F', 'F', 'F'],
                    
                   ['M', 'M', 'F', 'M', 'F', 'F'],
                   ['M', 'M', 'F', 'F', 'M', 'F'],
                   ['M', 'M', 'F', 'F', 'F', 'M'],
                    
                   ['M', 'F', 'M', 'M', 'F', 'F'],
                   ['M', 'F', 'M', 'F', 'M', 'F'],
                   ['M', 'F', 'M', 'F', 'F', 'M'],  
                     
                   ['M', 'F', 'F', 'M', 'M', 'F'],
                   ['M', 'F', 'F', 'M', 'F', 'M'],
                   ['M', 'F', 'F', 'F', 'M', 'M'],
                    
                    
                   ['F', 'M', 'M', 'M', 'F', 'F'],
                   ['F', 'M', 'M', 'F', 'M', 'F'],
                   ['F', 'M', 'M', 'F', 'F', 'M'],
                    
                   ['F', 'M', 'F', 'M', 'M', 'F'],
                   ['F', 'M', 'F', 'M', 'F', 'M'],
                   ['F', 'M', 'F', 'F', 'M', 'M'],
                     
                   ['F', 'F', 'M', 'M', 'M', 'F'],
                   ['F', 'F', 'M', 'M', 'F', 'M'],
                   ['F', 'F', 'M', 'F', 'M', 'M'],
                     
                   ['F', 'F', 'F', 'M', 'M', 'M']]
                   
                   )
```


```python
sample_mf.shape # get the shape of sample_mf
```




    (20, 6)




```python
sample_length= len(sample_mf)
print(sample_length)
```

    20


#### 1. Calculate the probability that at least 2 out of the first 3 appointments are with female students

First, select the first 3 appointment slots and check for "F".


```python
first_3_F = np.isin(sample_mf[:, 0:3], 'F')
first_3_F
```




    array([[False, False, False],
           [False, False,  True],
           [False, False,  True],
           [False, False,  True],
           [False,  True, False],
           [False,  True, False],
           [False,  True, False],
           [False,  True,  True],
           [False,  True,  True],
           [False,  True,  True],
           [ True, False, False],
           [ True, False, False],
           [ True, False, False],
           [ True, False,  True],
           [ True, False,  True],
           [ True, False,  True],
           [ True,  True, False],
           [ True,  True, False],
           [ True,  True, False],
           [ True,  True,  True]])




```python
num_F = np.sum(first_3_F, axis=1)
print(num_F)
```

    [0 1 1 1 1 1 1 2 2 2 1 1 1 2 2 2 2 2 2 3]



```python
F_2plus = np.sum(num_F >1)
print(F_2plus)
```

    10



```python
prob_F_2plus = F_2plus / sample_length
print(prob_F_2plus)
```

    0.5


#### 2. Calculate the probability that after 4 appointment slots, all the female students have had an appointment


```python
first_4_F = np.isin(sample_mf[:, 0:4], 'F')
first_4_F
```




    array([[False, False, False,  True],
           [False, False,  True, False],
           [False, False,  True,  True],
           [False, False,  True,  True],
           [False,  True, False, False],
           [False,  True, False,  True],
           [False,  True, False,  True],
           [False,  True,  True, False],
           [False,  True,  True, False],
           [False,  True,  True,  True],
           [ True, False, False, False],
           [ True, False, False,  True],
           [ True, False, False,  True],
           [ True, False,  True, False],
           [ True, False,  True, False],
           [ True, False,  True,  True],
           [ True,  True, False, False],
           [ True,  True, False, False],
           [ True,  True, False,  True],
           [ True,  True,  True, False]])




```python
num_F_4 = np.sum(first_4_F, axis=1)
print(num_F_4)
```

    [1 1 2 2 1 2 2 2 2 3 1 2 2 2 2 3 2 2 3 3]



```python
F_2plus_4 = np.sum(num_F_4 >2)
print(F_2plus_4)
```

    4



```python
prop_2plus_4 = F_2plus_4 / sample_length
print(prop_2plus_4)
```

    0.2


You noticed that coming up with the sample space was probably the most time-consuming part of the exercise, and it would really become unfeasible to write this down for say, 10 or, even worse, 20 appointments in a row. You'll learn about methods that make this easy in the next lecture!

## The Addition Law of Probability
At a supermarket, we randomly select customers, and make notes of whether a certain customer owns a Visa card (event A) or an Amex credit card (event B). Some customers own both cards.
You can assume that:

- P(A) = 0.5
- P(B) = 0.4
- both A and B = 0.25.

1) compute the probability that a selected customer has at least one credit card.

90 - 25 = 65 % (because owning both cards is already counted in owning either one)

2) compute the probability that a selected customer doesn't own any of the mentioned credit cards.

35 % (sample space is owning either card (answer in 1) or not, so 1-65 = answer.

3) compute the probability that a customer *only* owns VISA card.

prob(A) - P(A & B) = .5 - .25 = 25%

(You can use python here, but you don't have to)

## Summary

In this lab, you got to practice your knowledge on the foundations of probability through working on problems regarding the law of relative frequency, the probability axioms and the addition law of probability.
