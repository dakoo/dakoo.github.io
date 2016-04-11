---
layout: post
title: python - standard sequence 
description: python - standard sequence 
modified: 2016-04-10
tags: [python]
comments: true
image:
  feature: abstract-11.png
---
The following is the example of the implementation of stadard sequence in python.
Using __len()__ and __getitem__ method in a python class, standard sequence could be implemented. 

```python
''' --- The implementation of FrechDeck --- '''
import collections

Card = collections.namedtuple('Card', ['rank', 'suit'])

# By implementing __len__ and __getitem__, it works like a standard sequence
class FrenchDeck(object):
	ranks = [str(n) for n in range(2, 11)] + list('JQKA')
	suits = 'spades diamonds clubs hearts'.split()

	def __init__(self):
		self._cards = [Card(rank, suit) for suit in self.suits for rank in self.ranks]

	def __len__(self):
		return len(self._cards)

	def __getitem__(self, position):
		return self._cards[position]


''' --- Test --- '''

beer_card = Card('7', 'diamonds')
print beer_card  #Output: Card('7', 'diamonds')

deck = FrenchDeck()
print len(deck) #Output: 52 , __len()__ of FrenchDeck invoked

# By __getitem__ method, random access is supported
from random import choice
print choice(deck) 
print choice(deck)
print choice(deck) 

''' Output: 
Card(rank='Q', suit='spades')
Card(rank='5', suit='spades')
Card(rank='7', suit='diamonds')
'''
# By __getitem__ method, slicing supported
print deck[:3] #Output: [Card(rank='2', suit='spades'), Card(rank='3', suit='spades'), Card(rank='4', suit='spades')]

# By __getitem__ method, iteration supported
for card in deck:
	print card
''' Output: 
Card(rank='2', suit='spades')
Card(rank='3', suit='spades')
....
Card(rank='K', suit='hearts')
Card(rank='A', suit='hearts')
'''

for card in reversed(deck):
	print card
''' Output: 
Card(rank='A', suit='hearts')
Card(rank='K', suit='hearts')
...
Card(rank='3', suit='spades')
Card(rank='2', suit='spades')
'''
print Card('Q', 'diamonds') in deck #Output: True
print Card('Q', 'beasts') in deck #Output: False

# By __getitem__, sort() supported
suit_values = dict(spades=3, hearts=2, diamonds=1, clubs=0)
def rank_high(card):
    rank_value = FrenchDeck.ranks.index(card.rank) # rank_value: 2, 3, 4 ... J Q K A
    return rank_value * len(suit_values) + suit_values[card.suit] # rank_value has bigger weight than card.suit
for card in sorted(deck, key=rank_high): #Lowest key is first
	print card
''' Output: 
Card(rank='2', suit='clubs')
Card(rank='2', suit='diamonds')
...
Card(rank='A', suit='hearts')
Card(rank='A', suit='spades')
'''
for card in sorted(deck, key=rank_high, reverse=True):
	print card
''' Output: 
Card(rank='A', suit='spades')
Card(rank='A', suit='hearts')
...
Card(rank='2', suit='diamonds')
Card(rank='2', suit='clubs')
'''

```
