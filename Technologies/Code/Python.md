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


```

# Troubleshoot Installs
```bash 
python2 -c "import sys; print(sys.path)"
```


#data #code 