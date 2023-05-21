---
title: YAML
description: YAML/YML Notes
publish: true
permalink:YAML
tag: YAML
image: "Assets/innerarity.png"
---
# YAML
## YAML sample syntax
```yml
Servers:
	- name: Server1
	  owner: John
	  created: 11222023
	  status: active
```
see the same content in [[JSON#JSON sample syntax]] and [[XML#XML sample syntax]]

## Key Value Pair in YAML
```yml
Fruit: Apple
Vegetable: Carrot
Liquid: Water
Meat: Chicken
```

## Array/List in YAML
Ordered
```yml
Fruits: 
- Orange
- Apple
- Banana
Vesgetables:
- Carrot
- Cauliflower
- Tomato
```

## Dictionary/Map in YAML
Unordered
```yml
Banana:
	Calories: 105
	Fat: 0.4 g
	Carbs: 27 g
Grapes:
	Calories: 62
	Fat: .5 g
	Carbs: 25 g
```

# Dictionary, List, List of Dictionaries
Dictionary is an unordered list. 
Lists are ordered and must match. 
```yml
# dictionary
Color: Blue
Model:
	Name: Corvette
	Year: 1995
Transmission: Manual
Price: $20,000

# list
- Blue Corvette
- Grey Corvette
- Red Corvette
- Green Corvette
- Black Corvette

# list of dictionaries
- Color: Blue
  Model:
	  Name: Corvette
	  Model: 1995
  Transmission: Manual
  Price: $20,000

- Color: Red
  Model:
	  Name: Corvette
	  Model: 1995
  Transmission: Automatic
  Price: $20,000
  
# using different content for a list of dictionaries
employees:
  - name: Sarah
    hair_color: blonde
    height: 86
  - name: Fred
    hair_color: brown
    height: 72
```

# building a deployment file with YAML for K8S
![[Kubernetes (k8s)#required fields]]