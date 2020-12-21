---
title: Damage Calculator
description: Damage calculator for Dungeons and Dragons
permalink: /projects/dndcalculator
---
## Dungeons and Dragons Damage Calculator

```py
import random

def get_damage(attack, defense):
  """Get the value of damage based on roll strings."""

  attack = get_roll(attack)
  defense = get_roll(defense)

  if defense > attack:
    damage = 0
  else:
    damage = attack - defense

  return damage

def get_roll(rollstring):
  """Get the value of dice rolls."""

  rollstring_split = rollstring.split("d")

  total = 0
  
  z = 0
  while z < int(rollstring_split[0]):

    total += random.randint(1, int(rollstring_split[1]))

    z += 1
  
  return total

def main_menu():
  """Ask user how many rolls, input attack and defense dice for each roll, and display each roll round and respective damage."""

  num_rolls = int(input("How many rolls do you want to take? "))
  
  attack_rolls = []
  defense_rolls = []
  
  x = 1
  while x <= num_rolls:
    
    rolls = input("Input attack and defense roll " + str(x) + ": ")
    
    rolls_split = rolls.split(",")
    attack_rolls.append(rolls_split[0])
    defense_rolls.append(rolls_split[1])
    
    x += 1
  
  y = 0
  while y < num_rolls:
    
    print("Attack:" + attack_rolls[y] + ", Defense:" + defense_rolls[y] + " : Damage: " + str(get_damage(attack_rolls[y],defense_rolls[y])))

    y += 1

main_menu()
```
