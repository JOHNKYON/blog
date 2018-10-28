---
title: Data Model in Python
tags:
  - Python
  - Engineer
  - Programming
categories:
  - Computer Science
date: 2018-10-28 23:36:18
---


> One of the best qualities of Python is its consistency.
<p align="right">Luciano Ramalho, author of Fluent Python: Clear, Concise, and Effective Programming</p>

By the first time I read this sentence, all I felt was wired. As we all know, Python is a dynamically-typed language, you can never succeed in guessing the class or type of a variable. So at least, in terms of type, Python is far from consistent. However, soon I understand the meaning of consistency in Python: Nothing is special. Here, we're going to see several examples and understand what *"consistency"* really means in Python.

> Note: In Python, we're going to meet a lot of methods that looks like **\_\_getitem\_\_**, and we'll call them *"dunder_getitem"* (dunder means double-under). And also notice that, double under like **\_\_x** has other meanings in Python, and please don't mess up with them.

It's very important to implement those methods that can be called by the framework itself, we're going to show you how to implement two special methods called **\_\_getitem\_\_** and **\_\len\_\_**.

## 1.1 Pythonic Cards

Code 1-1: Cards with order.
```python
import collections

card = collections.namedtuple('Card', ['rank', 'suit'])

class FrenchDeck:
    ranks = [str(n) for n in range(2, 11)] + list('JQKA')
    suits = 'spades diamonds clubs hearts'.split()
    
    def __init__(self):
        self._cards = [Card(rank, suit) for suit in self.suits
                                        for rank in self.ranks]
    
    def __len__(self):
        return len(self._cards)
    
    def __getitem__(self, position):
        return self._cards[position]

```

Now, let's look at the **FrenchDeck** class. It's short and powerful. First of all, like any Python standard collection class, we can use **len()** function to check how many cards in it:
```python
>>> deck = FrenchDeck()
>>> len(deck)
52
```
We can also pick any specific card in the cards using **[ ]** like the following code:
```python
>>> deck[0]
Card(rank='2', suit='spades')
>>> deck[-1]
Card(rank='A', suit='hearts')
```

The more interesting thing is that, we don't need to write a new function to randomly pick a card, Python already had a function called **random.choice** to do the job, and we can just use it on the instance of our cards.

```python
>>> from random import choice
>>> choice(deck)
Card(rank='4', suit='spades')
```

See! We already benefit from implementing special methods to use Python data model:
- The users of your class doesn't need to remember many different names of a standard manipulation (**sizeof()** or **length()** or something else).
- Just use Python standard library, no need for re-inventing the wheels.

Even more, since **\_\_getitem\_\_** hands **[ ]** manipulation to **self._cards** list, so our **deck** class automatically supports slicing. See below:

```python
>>> deck[: 3]
[Card(rank='2', suit='spades'),
 Card(rank='3', suit='spades'),
 Card(rank='4', suit='spades')]
>>> deck[12:: 13]
[Card(rank='A', suit='spades'),
 Card(rank='A', suit='diamonds'),
 Card(rank='A', suit='clubs'),
 Card(rank='A', suit='hearts')]
```
The second function means that, pick all cards that has index 12, and pick one in every 13 cards.

And even more! Just by implementing **\_\_getitem\_\_** method, the cards become iterable:
```python
>>> for card in deck: # doctest: +ELLIPSIS
...     print(card)
Card(rank='2', suit='spades')
Card(rank='3', suit='spades')
Card(rank='4', suit='spades')
Card(rank='5', suit='spades')
...
```

Just by implementing **\_\_getitem\_\_** and **\_\len\_\_** methods, **FrenchDeck** class is just like any sequential data model in Python, showing core features of Python (like iterating and slicing). It can also be used for standard libraries like **random.choice**, **reversed** and **sorted**.

Here, we see how consistency exists in Python, and definitely we can write more elegant and pythonic code by implementing these special methods.

However, our cards still cannot be shuffled so far. We'll not talk about it now, although it's easy enough to be implemented by a single line of code in **\_\_setitem\_\_**, it influences more important things and we'll talk about them together.