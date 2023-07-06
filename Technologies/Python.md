# Python
## Versions
Python2 (2000-2010) - dated, and not compatible with Python3 Interpreter.
Python3 (2008-Current)

### Install and Verify Installation 
```bash
yum install python36
python3 -V
```

## Commands 
```bash
# runs an application in Python
python2 /some/path/main.py 

python3 /some/path/main.py
```

# Python Package Manager (pip)
```bash
# Python2 pip
python2 -V
pip2 -V

# Python3 pip
python3 -V
pip3 -V

# If you're unsure of which python version pip is tied to:
pip -V 

# install flask with pip
pip install flask 

# show application install and details
pip show flask 

# Use a single requirements document to manage dependences
pip install -r requirements.txt 

# sample requirements file with application versions specified
cat requirements.txt 
> Flask==0.10.1
> Jinja2==2.7.3
> MarkupSafe==0.23
> etc...

# upgrade
pip install flask --upgrade

# uninstall
pip uninstall flask -y
```

# Python Basics
## Variables
```python
message = "Hello, World!"
print(message)
# Hello, World!

message = 'Hello, "world!", is allowed!'
print(message)
# Hello, "world!", is allowed!

```
## Dynamic Variables
```python
first_name = "Gary"
print(f"{first_name}, how are you?")
# Gary, how are you? 
```

## Integers and Floats
Integers are rounded numbers: 
```python
2 + 3
# 5 

3 * 3
# 9

10 ** 6 # 10 to the power of 6
# 1000000
```

Floats are decimal numbers
```python
0.1 + 0.1
# 0.2 
```

## Methods
###  Title
```python
name = "ada lovelace"
print(name.title())
# Ada Lovelace
```

### UPPER
```python
name = "ada lovelace"
print(name.UPPER())
print(name.lower())
# ADA LOVELACE 
# ada lovelace
```

## f string
f-strings = format; because python will format the string. 
```python
first_name = "ada"
last_name = "lovelace"
full_name = f"{first_name} {last_name}"
print(full_name)
# ada lovelace

print(f"hello, {first_name.title()}")
# hello, Ada
```

## Lists
Lists are indexes of items, starting at 0, not 1. 
```python
bicycles = ['trek', 'cannondale', 'redline']
print(bicycles[0])
# trek

print(bicycles[1])
# cannondale

print(bicycles[1].title)
# Cannondale 

bicycles = ['trek', 'cannondale', 'redline']
message = f"My favorite bike is a {bicycles[0].title()}."

print(message)
```

## Append
```python
motorcycles = ['honda', 'yamaha']
print(motorcycles)

# append, or add the ducati to the list. 
motorcycles.append('ducati')
print(motorcycles)
```

## Insert
Insert is different from append, in the fact that you can insert it into a specified position, rather than appending it to the end of the list. 
```python
motorcycles = ['honda', 'yamaha']
print(motorcycles)

motorcycles.insert(0, 'ducati')
print(motorcycles)
# ducati, honda, yamaha. 
```

## Delete
```python
motorcycles = ['honda', 'yamaha']
print(motorcycles)

del motorcycles[1]
print(motorcycles)
# the yamaha cycle will be deleted. 
```

### Remove
```python
motorcycles.remove('ducati')
print(motorcycles)
# removes the name explicitly 
```

### pop
The pop method is great for identifying the "last" object touched. The last purchased, the last added items. 
```python
motorcycles = ['honda', 'yamaha']
print(motorcycles)

popped_motorcycle = motorcycle.pop()
print(motorcycles)
print(popped_motorcycle) # prints the, off the bottom, popped name. 
```

adding a positional statement to the pop method will allow you to select the first item:
```python
motorcycle.pop(0)
# this will select the first name to pop. 
```

---

# Troubleshoot Installs
```bash 
python2 -c "import sys; print(sys.path)"
```


#data #code 