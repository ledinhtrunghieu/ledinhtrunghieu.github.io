---
layout: post
author: ledinhtrunghieu
title: Lesson 5 - Writing Functions in Python
---
    
# 1. Best Practices

Learn about Docstrings, why they matter, how to know when you need to turn a chunk of code into a function and the details of how Python passes arguments to functions.

## 1.1. Docstrings

**A Complex function**
```python
"""Split a DataFrame's columns into two halves and then stack   them vertically, returning a new DataFrame with `new_names` as the column names.

Args:
df (DataFrame): The DataFrame to split.
new_names (iterable of str): The column names for the new DataFrame.

Returns:
DataFrame """

def split_and_stack(df, new_names): 
    half = int(len(df.columns) / 2) 
    left = df.iloc[:, :half]
    right = df.iloc[:, half:] 
    return pd.DataFrame(
        data=np.vstack([left.values, right.values]), 
        columns=new_names
)
```

With a **docstring** though, it is much easier to tell what the expected inputs and outputs should be, as well as what the function does. This makes it easier for you and other engineers to use your code in the future.

```python
def function_name(arguments):
    """
    Description	of	what the function does.
    
    Description	of	the arguments, if any.

    Description	of	the return value(s), if any.

    Description	of	errors raised, if any.

    Optional extra notes or examples of usage. 
    """
```

A **docstring** is a string written as the first line of a function. Because docstrings usually span multiple lines, they are enclosed in triple quotes, Python's way of writing multi-line strings. Every docstring has some (although usually not all) of these five key pieces of information: what the function does, what the arguments are, what the return value or values should be, info about any errors raised, and anything else you'd like to say about the function.

**Docstring format**
* Google Style
* Numpydoc
* reStructuredText
* EpyText

**Google Style - description**

```python
def function(arg_1, arg_2=42): 
    """Description of what the function does.

    Args:
    arg_1 (str): Description of arg_1 that can break onto the next line if needed.
    arg_2 (int, optional): Write optional when an argument has a default value.

    Returns:
    bool: Optional description of the return value 
    Extra lines are not indented.

    Raises:
    ValueError: Include any error types that the function intentionally raises.

    Notes:
    See https://www.datacamp.com/community/tutorials/docstrings-python for 

```

* In Google style, the docstring starts with a concise description of what the function does. This should be in imperative language. For instance: "Split the data frame and stack the columns" instead of "This function will split the data frame and stack the columns".
* Next comes the "Args" section where you list each argument name, followed by its expected type in parentheses, and then what its role is in the function.
* If an argument has a default value, mark it as "optional" when describing the type. If the function does not take any parameters, feel free to leave this section out.
* "Returns" section is where you list the expected type or types of what gets returned. You can also provide some comment about what gets returned, but often the name of the function and the description will make this clear. Additional lines should not be indented.
* Finally, if your function intentionally raises any errors, you should add a "Raises" section. You can also include any additional notes or examples of usage in free form text at the end.

**Numpydoc**

The Numpydoc format is very similar and is the most common format in the scientific Python community

```python
def function(arg_1, arg_2=42):
    """
    Description of what the function does. 
    
    Parameters
    ----------
    arg_1 : expected type of arg_1 
        Description of arg_1.
    arg_2 : int, optional
        Write optional when an argument has a default value. 
        Default=42.

    Returns
    -------
    The type of the return value
        Can include a description of the return value.
        Replace "Returns" with "Yields" if this function is a generator. """
    """
```

**Retrieving docstrings**

```python
def the_answer():
    """Return the answer to life, the universe, and everything.
    
    Returns: int
    """
return 42
print(the_answer.__doc__)
```
<img src="/assets/images/20210426_FunctionsInPython/pic1.png" class="largepic"/>


```python
import inspect 
print(inspect.getdoc(the_answer))
```
<img src="/assets/images/20210426_FunctionsInPython/pic2.png" class="largepic"/>

```python
def count_letter(content, letter):
  """Count the number of times `letter` appears in `content`.

  Args:
    content (str): The string to search.
    letter (str): The letter to search for.

  Returns:
    int

  # Add a section detailing what errors might be raised
  Raises:
    ValueError: If `letter` is not a one-character string.
  """
  if (not isinstance(letter, str)) or len(letter) != 1:
    raise ValueError('`letter` must be a single character string.')
  return len([char for char in content if char == letter])
```

```python
import inspect

def build_tooltip(function):
  """Create a tooltip for any function that shows the
  function's docstring.

  Args:
    function (callable): The function we want a tooltip for.

  Returns:
    str
  """
  # Get the docstring for the "function" argument by using inspect
  docstring = inspect.getdoc(function)
  border = '#' * 28
  return '{}\n{}\n{}'.format(border, docstring, border)

print(build_tooltip(count_letter))
print(build_tooltip(range))
print(build_tooltip(print))
```


## 1.2. DRY and "Do One Thing"

**Don't repeat yourself (DRY)**

```python
train = pd.read_csv('train.csv')
train_y = train['labels'].values
train_X = train[col for col in train.columns if col != 'labels'].values 
train_pca = PCA(n_components=2).fit_transform(train_X) 
plt.scatter(train_pca[:,0], train_pca[:,1])
```

```python
val = pd.read_csv('validation.csv') 
val_y = val['labels'].values
val_X = val[col for col in val.columns if col != 'labels'].values 
val_pca = PCA(n_components=2).fit_transform(val_X) 
plt.scatter(val_pca[:,0], val_pca[:,1])
```

```python
test = pd.read_csv('test.csv') 
test_y = test['labels'].values
test_X = test[col for col in test.columns if col != 'labels'].values 
test_pca = PCA(n_components=2).fit_transform(train_X) 
plt.scatter(test_pca[:,0], test_pca[:,1])
```

**Use functions to avoid repetition**

```python
def load_and_plot(path):
    """Load a data set and plot the first two principal components.

    Args:
        path (str): The location of a CSV file.

    Returns:
        tuple of ndarray: (features, labels) 
    """
    # load the data
    data = pd.read_csv(path) 
    y = data['label'].values
    X = data[col for col in train.columns if col != 'label'].values 

    # plot the first two principle components 
    pca = PCA(n_components=2).fit_transform(X) 

    # return loaded data 
    plt.scatter(pca[:,0], pca[:,1])
    return X, y
```

```python
train_X, train_y = load_and_plot('train.csv') 
val_X, val_y = load_and_plot('validation.csv') 
test_X, test_y = load_and_plot('test.csv')
```

Every function should have a single responsibility. Instead of one big function, we could have a more nimble function that just loads the data and a second one for plotting. Imagine that later on in your script, you just want to load the data and not plot it. 

```python
def load_data(path):
    """Load a data set.
    Args:
    path (str): The location of a CSV file.
    Returns:
    tuple of ndarray: (features, labels) 
    """
    data = pd.read_csv(path) 
    y = data['labels'].values
    X = data[col for col in data.columns 
        if col != 'labels'].values
return X, y
```

```python
def plot_data(X):
    """Plot the first two principal components of a matrix.
    
    Args:
        X (numpy.ndarray): The data to plot. 
    """
    pca = PCA(n_components=2).fit_transform(X)
    plt.scatter(pca[:,0], pca[:,1])
```

**Advantages of doing one thing**
* More flexible
* More easily understood
* Simpler to test
* Simpler to debug
* Easier to change

**Extract a function**
```python
# Standardize the GPAs for each year
df['y1_z'] = (df.y1_gpa - df.y1_gpa.mean()) / df.y1_gpa.std()
df['y2_z'] = (df.y2_gpa - df.y2_gpa.mean()) / df.y2_gpa.std()
df['y3_z'] = (df.y3_gpa - df.y3_gpa.mean()) / df.y3_gpa.std()
df['y4_z'] = (df.y4_gpa - df.y4_gpa.mean()) / df.y4_gpa.std()
```

```python
def standardize(column):
  """Standardize the values in a column.

  Args:
    column (pandas Series): The data to standardize.

  Returns:
    pandas Series: the values as z-scores
  """
  # Finish the function so that it returns the z-scores
  z_score = (column - column.mean()) / column.std()
  return z_score

# Use the standardize() function to calculate the z-scores
df['y1_z'] = standardize(df.y1_gpa)
df['y2_z'] = standardize(df.y2_gpa)
df['y3_z'] = standardize(df.y3_gpa)
df['y4_z'] = standardize(df.y4_gpa)
```

**Split up the a function**
```python
def mean_and_median(values):
  """Get the mean and median of a sorted list of `values`

  Args:
    values (iterable of float): A list of numbers

  Returns:
    tuple (float, float): The mean and median
  """
  mean = sum(values) / len(values)
  midpoint = int(len(values) / 2)
  if len(values) % 2 == 0:
    median = (values[midpoint - 1] + values[midpoint]) / 2
  else:
    median = values[midpoint]

  return mean, median
```
```python
def mean(values):
  """Get the mean of a sorted list of values

  Args:
    values (iterable of float): A list of numbers

  Returns:
    float
  """
  # Write the mean() function
  mean = sum(values) / len(values)
  return mean

def median(values):
  """Get the median of a sorted list of values

  Args:
    values (iterable of float): A list of numbers

  Returns:
    float
  """
  # Write the median() function
  midpoint = int(len(values) / 2)
  if len(values) % 2 == 0:
    median = (values[midpoint - 1] + values[midpoint]) / 2
  else:
    median = values[midpoint]
  return median
```


## 1.3. Pass by assignment

<img src="/assets/images/20210426_FunctionsInPython/pic2.png" class="largepic"/>

In Python, integers are immutable, meaning they can't be changed.

<img src="/assets/images/20210426_FunctionsInPython/pic19.png" class="largepic"/>

we created a variable "my_var" and assigned it the value 3. Then we passed it to the function bar(), which caused the argument "x" to point to the same place "my_var" is pointing. But the bar() function assigns "x" to a new value, so the "my_var" variable isn't touched. In fact, there is no way in Python to have changed "x" or "my_var" directly, because integers are immutable variables.

<img src="/assets/images/20210426_FunctionsInPython/pic4.png" class="largepic"/>

There are only a few immutable data types in Python because almost everything is represented as an object. The only way to tell if something is mutable is to see if there is a function or method that will change the object without assigning it to a new variable.

```python
def store_lower(_dict, _string):
  """Add a mapping between `_string` and a lowercased version of `_string` to `_dict`

  Args:
    _dict (dict): The dictionary to update.
    _string (str): The string to add.
  """
  orig_string = _string
  _string = _string.lower()
  _dict[orig_string] = _string

d = {}
s = 'Hello'

store_lower(d, s)
```

```
d = {'Hello': 'hello'}, s = 'Hello'

Dictionaries are mutable objects in Python, so the function can directly change it in the `_dict[_orig_string] = _string` statement. Strings, on the other hand, are immutable. When the function creates the lowercase version, it has to assign it to the `_string` variable. This disconnects what happens to `_string` from the external `s` variable.
```


**Best practice for default arguments**
One of your co-workers (who obviously didn't take this course) has written this function for adding a column to a pandas DataFrame. Unfortunately, they used a mutable variable as a default argument value! Please show them a better way to do this so that they don't get unexpected behavior.

```python
def add_column(values, df=pandas.DataFrame()):
  """Add a column of `values` to a DataFrame `df`.
  The column will be named "col_<n>" where "n" is
  the numerical index of the column.

  Args:
    values (iterable): The values of the new column
    df (DataFrame, optional): The DataFrame to update.
      If no DataFrame is passed, one is created by default.

  Returns:
    DataFrame
  """
  df['col_{}'.format(len(df.columns))] = values
  return df

# Use an immutable variable for the default argument
def better_add_column(values, df=None):
  """Add a column of `values` to a DataFrame `df`.
  The column will be named "col_<n>" where "n" is
  the numerical index of the column.

  Args:
    values (iterable): The values of the new column
    df (DataFrame, optional): The DataFrame to update.
      If no DataFrame is passed, one is created by default.

  Returns:
    DataFrame
  """
  # Update the function to create a default DataFrame
  if df is None:
    df = pandas.DataFrame()
  df['col_{}'.format(len(df.columns))] = values
  return df
```


# 2. Context Managers

Context managers are a convenient way to provide connections in Python and guarantee that those connections get cleaned up when you are done using them. 

## 2.1. Using context managers

**What is a context manager**
A context manager:
* Sets up a context 
* Runs your code 
* Removes the context

**A real-world example**
```python
with open('my_file.txt') as my_file: 
    text = my_file.read()
    length = len(text)

print('The file is {} characters long'.format(length))
```

`open` does three things:
* Sets up a context by opening a file
* Lets you run any code you want on that file 
* Removes the context by closing the file

**Using a context manager**
```python
with <context-manager>(<args>) as <variable-name>: 
    # Run your code here
    # This code is running "inside the context"

# This code runs after the context is removed
```

**Practice**
```python
# Open "alice.txt" and assign the file to "file"
with open('alice.txt') as file:
  text = file.read()

n = 0
for word in text.split():
  if word.lower() in ['cat', 'cats']:
    n += 1

print('Lewis Carroll uses the word "cat" {} times'.format(n))
```

```python
image = get_image_from_instagram()

# Time how long process_with_numpy(image) takes to run
with timer():
  print('Numpy version')
  process_with_numpy(image)

# Time how long process_with_pytorch(image) takes to run
with timer():
  print('Pytorch version')
  process_with_pytorch(image)

<script.py> output:
    Numpy version
    Processing..........done!
    Elapsed: 1.52 seconds
    Pytorch version
    Processing..........done!
    Elapsed: 0.33 seconds
```

## 2.2. Writing context managers

**Two ways to define a context manager**
* Class-based 
* Function-based

```python
@contextlib.contextmanager 
def my_context():
    # Add any set up code you need yield
    yield
    # Add any teardown code you need
```

1.	Define a function.
2.	(optional) Add any set up code your context needs.
3.	Use the "yield" keyword.
4.	(optional) Add any teardown code your context needs.
5.	Add the `@contextlib.contextmanager` decorator.


**The "yield" keyword**

```python
@contextlib.contextmanager 
def my_context():
    print('hello') 
    yield 42 
    print('goodbye')
```

```python
with my_context() as foo: 
    print('foo is {}'.format(foo))
```

<img src="/assets/images/20210426_FunctionsInPython/pic5.png" class="largepic"/>

The "yield" keyword may also be new to you. When you write this word, it means that you are going to return a value, but you expect to finish the rest of the function at some point in the future. The value that your context manager yields can be assigned to a variable in the "with" statement by adding "as <variable name>". Here, we've assigned the value 42 that my_context() yields to the variable "foo". By running this code, you can see that after the context block is done executing, the rest of the my_context() function gets run, printing "goodbye". Some of you may recognize the "yield" keyword as a thing that gets used when creating generators. In fact, a context manager function is technically a generator that yields a single value.


**Setup and teardown**

```python
@contextlib.contextmanager 
def database(url):
    # set up database connection
    db = postgres.connect(url)

    yield db

    # tear down database connection
    db.disconnect()
```

```python
url = 'http://datacamp.com/data' 
with database(url) as my_db:
    course_list = my_db.execute( 
        'SELECT * FROM courses'
)

```

**Yielding a value or None**
```python
@contextlib.contextmanager 
    def in_dir(path):
    # save current working directory
    old_dir = os.getcwd()

    # switch to new working directory
    os.chdir(path)

    yield

    # change back to previous # working directory
    os.chdir(old_dir)
```

```python
with in_dir('/data/project_1/'): 
    project_files = os.listdir()
```

**Practice**
```python
# Add a decorator that will make timer() a context manager
@contextlib.contextmanager
def timer():
  """Time the execution of a context block.

  Yields:
    None
  """
  start = time.time()
  # Send control back to the context block
  yield
  end = time.time()
  print('Elapsed: {:.2f}s'.format(end - start))

with timer():
  print('This should take approximately 0.25 seconds')
  time.sleep(0.25)

<script.py> output:
    This should take approximately 0.25 seconds
    Elapsed: 0.25s
```

**A read-only open() context manager**

```
The regular open() context manager:

takes a filename and a mode ('r' for read, 'w' for write, or 'a' for append)
opens the file for reading, writing, or appending
yields control back to the context, along with a reference to the file
waits for the context to finish
and then closes the file before exiting
Your context manager will do the same thing, except it will only take the filename as an argument and it will only open the file for reading.
```

```python
@contextlib.contextmanager
def open_read_only(filename):
  """Open a file in read-only mode.

  Args:
    filename (str): The location of the file to read

  Yields:
    file object
  """
  read_only_file = open(filename, mode='r')
  # Yield read_only_file so it can be assigned to my_file
  yield read_only_file
  # Close read_only_file
  read_only_file.close()

with open_read_only('my_file.txt') as my_file:
  print(my_file.read())

<script.py> output:
    Congratulations! You wrote a context manager that acts like "open()" but operates in read-only mode!
```

## 2.3. Advanced topics

**Nested context**

```python
def copy(src, dst):
    """Copy the contents of one file to another.

    Args:
        src (str): File name of the file to be copied. 
        dst (str): Where to write the new file.
    """
    # Open the source file and read in the contents 
    with open(src) as f_src:
        contents = f_src.read()

    # Open the destination file and write out the contents 
    with open(dst, 'w') as f_dst:
        f_dst.write(contents)
```

```python
def copy(src, dst):
    """Copy the contents of one file to another.

    Args:
        src (str): File name of the file to be copied. 
        dst (str): Where to write the new file.
    """
    # Open both files
    with open(src) as f_src:
        with open(dst, 'w') as f_dst:
        # Read and write each line, one at a time 
        for line in f_src:
            f_dst.write(line)
```

**Handling errors**
```python
def get_printer(ip):
    p = connect_to_printer(ip)

    # This MUST be called or no one else will 
    # be able to connect to the printer 
    p.disconnect()
    print('disconnected from printer')

doc = {'text': 'This is my text.'}

with get_printer('10.0.34.111') as printer:
    printer.print_page(doc['txt'])
```

<img src="/assets/images/20210426_FunctionsInPython/pic6.png" class="largepic"/>


This will raise a KeyError because "txt" is not in the "doc" dictionary. And that means "p.disconnect()" doesn't get called.

```python
try:
    # code that might raise an error 
except:
    # do something about the error 
finally:
    # this code runs no matter what
```

```python
def get_printer(ip):
    p = connect_to_printer(ip)

    try:
        yield 
    finally:
        p.disconnect()  
        print('disconnected from printer')

doc = {'text': 'This is my text.'}

with get_printer('10.0.34.111') as printer: 
    printer.print_page(doc['txt'])
```

<img src="/assets/images/20210426_FunctionsInPython/pic7.png" class="largepic"/>

When the sloppy programmer runs their code, they still get the KeyError, but "finally" ensures that "p.disconnect()" is called before the error is raised.


**Context Manager Pattern**

<img src="/assets/images/20210426_FunctionsInPython/pic8.png" class="largepic"/>

**Practice**

```python
# Use the "stock('NVDA')" context manager
# and assign the result to the variable "nvda"
with stock('NVDA') as nvda:
  # Open 'NVDA.txt' for writing as f_out
  with open('NVDA.txt', 'w') as f_out:
    for _ in range(10):
      value = nvda.price()
      print('Logging ${:.2f} for NVDA'.format(value))
      f_out.write('{:.2f}\n'.format(value))

Opening stock ticker for NVDA
Logging $140.39 for NVDA
Logging $140.33 for NVDA
Logging $140.27 for NVDA
Logging $140.23 for NVDA
Logging $140.20 for NVDA
Logging $140.28 for NVDA
Logging $140.34 for NVDA
Logging $140.39 for NVDA
Logging $140.29 for NVDA
Logging $140.27 for NVDA
Closing stock ticker
```

```python
def in_dir(directory):
  """Change current working directory to `directory`,
  allow the user to run some code, and change back.

  Args:
    directory (str): The path to a directory to work in.
  """
  current_dir = os.getcwd()
  os.chdir(directory)

  # Add code that lets you handle errors
  try:
    yield
  # Ensure the directory is reset,
  # whether there was an error or not
  finally:
    os.chdir(current_dir)
```

# 3. Decorators

## 3.1. Functions are objects

Decorators are an extremely powerful concept in Python. They allow you to modify the behavior of a function without changing the code of the function itself

**Functions are just another type of object**

Python objects:

```python
def x(): 
    pass
x = [1, 2, 3]
x = {'foo': 42}
x = pandas.DataFrame()
x	= 3
x	= 71.2
```

**Functions as variable**
```python
def my_function(): 
    print('Hello')
x = my_function 
type(x)
```

```
<type 'function'>
```

```python
x()
```

```
Hello
```

```python
PrintyMcPrintface = print 
PrintyMcPrintface('Python is awesome!')
```

```
Python is awesome!
```

**Lists and dictionaries of functions**

```python
list_of_functions = [my_function, open, print] 
list_of_functions[2]('I am printing with an element of a list!')
```

<img src="/assets/images/20210426_FunctionsInPython/pic9.png" class="largepic"/>

```python
dict_of_functions = { 
    'func1': my_function, 
    'func2': open, 
    'func3': print
}
dict_of_functions['func3']('I am printing with a value of a dict!')
```
<img src="/assets/images/20210426_FunctionsInPython/pic10.png" class="largepic"/>

**Defining a function inside another function**
```python
def foo(x,  y): 
    def in_range(v):
        return v > 4 and v < 10

    if in_range(x) and in_range(y):
        print(x * y)
```

**Functions as return values**
```python
def get_function(): 
    def print_me(s):
        print(s)

    return print_me

new_func = get_function() 
new_func('This is a sentence.')
```
<img src="/assets/images/20210426_FunctionsInPython/pic11.png" class="largepic"/>

**Practice**

```python
def has_docstring(func):
  """Check to see if the function 
  `func` has a docstring.

  Args:
    func (callable): A function.

  Returns:
    bool
  """
  return func.__doc__ is not None

# Call has_docstring() on the log_product() function
ok = has_docstring(log_product)

if not ok:
  print("log_product() doesn't have a docstring!")
else:
  print("log_product() looks ok")


def create_math_function(func_name):
  if func_name == 'add':
    def add(a, b):
      return a + b
    return add
  elif func_name == 'subtract':
    # Define the subtract() function
    def subtract(a,b):
      return a - b
    return subtract
  else:
    print("I don't know that one")
    
add = create_math_function('add')
print('5 + 2 = {}'.format(add(5, 2)))

subtract = create_math_function('subtract')
print('5 - 2 = {}'.format(subtract(5, 2)))

5 + 2 = 7
5 - 2 = 3
```

## 3.2. Scope

* Local Scope: First, the interpreter looks in the local scope. When you are inside a function, the local scope is made up of the arguments and any variables defined inside the function.
* If the interpreter can't find the variable in the local scope, it expands its search to the global scope. These are the things defined outside the function.
* Finally, if it can't find the thing it is looking for in the global scope, the interpreter checks the builtin scope. These are things that are always available in Python. For instance, the print() function is in the builtin scope, which is why we are able to use it in our foo() function.
* In the case of nested functions, where one function is defined inside another function, Python will check the scope of the parent function before checking the global scope. This is called the nonlocal scope to show that it is not the local scope of the child function and not the global scope.


**The global keyword**
Note that Python only gives you read access to variables defined outside of your current scope. If what we had really wanted was to change the value of x in the global scope, then we have to declare that we mean the global x by using the global keyword. 

**Understanding scope**
```python
x = 50

def one():
  x = 10

def two():
  global x
  x = 30

def three():
  x = 100
  print(x)

for func in [one, two, three]:
  func()
  print(x)


50, 30, 100, 30
```

**Modifying variables outside local scope**

```python
call_count = 0

def my_function():
  # Use a keyword that lets us update call_count 
  global call_count
  call_count += 1
  
  print("You've called my_function() {} times!".format(
    call_count
  ))
  
for _ in range(20):
  my_function()

You've called my_function() 1 times!
You've called my_function() 2 times!
You've called my_function() 3 times!
You've called my_function() 4 times!
You've called my_function() 5 times!
...
You've called my_function() 19 times!
You've called my_function() 20 times!
```


```python
def read_files():
  file_contents = None
  
  def save_contents(filename):
    # Add a keyword that lets us modify file_contents
    nonlocal file_contents
    if file_contents is None:
      file_contents = []
    with open(filename) as fin:
      file_contents.append(fin.read())
      
  for filename in ['1984.txt', 'MobyDick.txt', 'CatsEye.txt']:
    save_contents(filename)
    
  return file_contents

print('\n'.join(read_files()))


It was a bright day in April, and the clocks were striking thirteen.
Call me Ishmael.
Time is not a line but a dimension, like the dimensions of space.
```


```python
def wait_until_done():
  def check_is_done():
    # Add a keyword so that wait_until_done() 
    # doesn't run forever
    global done
    if random.random() < 0.1:
      done = True
      
  while not done:
    check_is_done()

done = False
wait_until_done()

print('Work done? {}'.format(done))

Work done? True
```



```python
x = 7

def foo(): 
    global x 
    x = 42
    print(x)
foo()

42

def foo():
    x = 10

    def bar(): x = 200
        print(x)

    bar()
    print(x)

foo()

200
10


def foo():
    def bar(): 
        nonlocal x 
        x = 200
        print(x)

    bar()
    print(x)

foo()

200
200

```

## 3.3. Closures

A **closure** in Python is a tuple of variables that are no longer in scope, but that a function needs in order to run.

**Attaching nonlocal variables to nested functions**

```python
def foo(): 
    a = 5
    def bar(): 
        print(a)
    return bar

func = foo()

func()

Result: 5

type(func.__closure__)

<class 'tuple'>

len(func.__closure__)

1

func.__closure__[0].cell_contents

5

del(x)
func()

5

```

* When `foo()` returned the new `bar()` function, Python helpfully attached any nonlocal variable that `bar()` was going to need to the function object. Those **variables** get stored in a tuple in the "__closure__" attribute of the function. The closure for "func" has one variable, and you can view the value of that variable by accessing the "cell_contents" of the item.
* Closure and deletion: Because foo()'s "value" argument gets added to the closure attached to the new "my_func" function. So even though x doesn't exist anymore, the value persists in its closure.

**Definitions - nested function**

Nested function: A function defined inside another function.

```python
# outer function 
def parent():
    # nested function 
    def child():
        pass 
    return child
```

**Definitions - nonlocal variables**

Nonlocal variables: Variables defined in the parent function that are used by the child function.

```python
def parent(arg_1, arg_2):
    # From child()'s point of view,
    # `value` and `my_dict` are nonlocal variables, 
    # as are `arg_1` and `arg_2`.
    value = 22
    my_dict = {'chocolate': 'yummy'}


    def child(): 
        print(2 * value)
        print(my_dict['chocolate']) 
        print(arg_1 + arg_2)


    return child
```

**Closure: Nonlocal variables a attached to a returned function.**

```python
def parent(arg_1, arg_2): 
    value = 22
    my_dict = {'chocolate': 'yummy'}


    def child(): 
        print(2 * value)
        print(my_dict['chocolate']) 
        print(arg_1 + arg_2)
    
    return  child 

new_function = parent(3, 4)

print([cell.cell_contents for cell in new_function.__closure__])
```

**Why does all of this matter?**

Decorators use:
* Functions as objects
* Nested functions
* Nonlocal scope
* Closures*

**Practice**

```python
def return_a_func(arg1, arg2):
  def new_func():
    print('arg1 was {}'.format(arg1))
    print('arg2 was {}'.format(arg2))
  return new_func
    
my_func = return_a_func(2, 17)

print(my_func.__closure__ is not None)
print(len(my_func.__closure__) == 2)

# Get the values of the variables in the closure
closure_values = [
  my_func.__closure__[i].cell_contents for i in range(2)
]
print(closure_values == [2, 17])

True
True
True
```

## 3.4. Decorators


<img src="/assets/images/20210426_FunctionsInPython/pic12.png" class="largepic"/>

A **decorator** is a **wrapper** that you can place around a function that **changes that function's behavior**. You can modify the **inputs**, modify the **outputs**, or even change the **behavior of the function** itself.

**What does a decorator look like?**

```python
@double_args
def multiply(a, b): 
    return a * b
multiply(1, 5)
```
<img src="/assets/images/20210426_FunctionsInPython/pic13.png" class="largepic"/>

**The double_args decorator**

```python
def multiply(a, b): 
    return a * b
def double_args(func): 
    def wrapper(a, b):
        # Call the passed in function, but double each argument
        return func(a * 2, b * 2) 
    return wrapper
new_multiply = double_args(multiply) 
new_multiply(1,	5)

20

multiply. __closure__[0].cell_contents

<function multiply at 0x7f0060c9e620>

```
 Remember that we can do this because Python stores the original multiply function in the new function's closure.

 **Decorator syntax**

```python
def double_args(func): 
    def wrapper(a, b):
        return func(a * 2, b * 2)
    return wrapper

@double_args
def multiply(a, b): 
    return a * b

multiply(1,	5)

20
```
**Practice**

```python
def print_before_and_after(func):
  def wrapper(*args):
    print('Before {}'.format(func.__name__))
    # Call the function being decorated with *args
    func(*args)
    print('After {}'.format(func.__name__))
  # Return the nested function
  return wrapper

@print_before_and_after
def multiply(a, b):
  print(a * b)

multiply(5, 10)
```

# 4. More on Decorators

Learn advanced decorator concepts like how to preserve the metadata of your decorated functions and how to write decorators that take arguments.

## 4.1. Real-world examples

**Time a function**

```python
import time
def timer(func):
    """A decorator that prints how long a function took to run.

    Args:
        func (callable): The function being decorated.

    Returns:
        callable: The decorated function. 
    """

    # Define the wrapper function to return.
    def wrapper(*args, **kwargs):
    # When wrapper() is called, get the current time. 
    t_start = time.time()
    # Call the decorated function and store the result. 
    result = func(*args, **kwargs)
    # Get the total time it took to run, and print it. 
    t_total = time.time() - t_start
    print('{} took {}s'.format(func.__name__,t_total)) 
    return result
return wrapper

```

**Using timer()**
```python
@timer
def sleep_n_seconds(n): 
    time.sleep(n)

sleep_n_seconds(5)
sleep_n_seconds took 5.0050950050354s

sleep_n_seconds(10)
sleep_n_seconds took 10.010067701339722s
```

**Memoize**

**Memoizing** is the process of storing the results of a function so that the next time the function is called with the same arguments; you can just look up the answer. 

```python
def memoize(func):
    """Store the results of the decorated function for fast lookup
    """
    # Store results in a dict that maps arguments to results
    cache = {}
    # Define the wrapper function to return.
    def wrapper(*args, **kwargs):
        # If these arguments haven't been seen before,
        if (args, kwargs) not in cache:
            # Call func() and store the result.
            cache[(args, kwargs)] = func(*args, **kwargs)
        return cache[(args, kwargs)]
    return wrapper
```

```python
@memoize
def slow_function(a, b): 
    print('Sleeping...') 
    time.sleep(5)
    return a + b

slow_function(3, 4)

Sleeping... 
7

slow_function(3, 4)
7
```

**When to use decorators**
Add common behavior to multiple functions

```python

@timer  
def foo():
    # do some computation

@timer  
def bar():
    # do some other computation


@timer  
def baz():
    # do something else
```
**Practice**

**Print the return type**
```python
def print_return_type(func):
  # Define wrapper(), the decorated function
  def wrapper(*args, **kwargs):
    # Call the function being decorated
    result = func(*args, **kwargs)
    print('{}() returned type {}'.format(
      func.__name__, type(result)
    ))
    return result
  # Return the decorated function
  return wrapper
  
@print_return_type
def foo(value):
  return value
  
print(foo(42))
print(foo([1, 2, 3]))
print(foo({'a': 42}))

<script.py> output:
    foo() returned type <class 'int'>
    42
    foo() returned type <class 'list'>
    [1, 2, 3]
    foo() returned type <class 'dict'>
    {'a': 42}
```
**Counter**
```python
def counter(func):
  def wrapper(*args, **kwargs):
    wrapper.count += 1
    # Call the function being decorated and return the result
    return func(*args, **kwargs)
  wrapper.count = 0
  # Return the new decorated function
  return wrapper

# Decorate foo() with the counter() decorator
@counter
def foo():
  print('calling foo()')
  
foo()
foo()

print('foo() was called {} times.'.format(foo.count))

calling foo()
calling foo()
foo() was called 2 times.
```

## 4.2. Decorators and metadata

```python
@timer
def sleep_n_seconds(n=10):
    """Pause processing for n seconds.
Args:
    n (int): The number of seconds to pause for. 
    """
    time.sleep(n)
print(sleep_n_seconds.__doc__)
```
<img src="/assets/images/20210426_FunctionsInPython/pic14.png" class="largepic"/>

```python
print(sleep_n_seconds.__name__)
```
```
wrapper
```
When we try to print the docstring, we get nothing back. Even stranger, when we try to look up the function's name, Python tells us that sleep_n_seconds()'s name is "wrapper".

**The timer decorator**

```python
def timer(func):
    """A decorator that prints how long a function took to run."""

    def wrapper(*args, **kwargs): 
        t_start = time.time()

        result = func(*args, **kwargs)
        t_total = time.time() - t_start
        print('{} took {}s'.format(func.__name__, t_total))
        
        return result
    return wrapper
```

When you ask for sleep_n_seconds()'s docstring or name, you are actually referencing the nested function that was returned by the decorator. In this case, the nested function was called wrapper() and it didn't have a docstring.

```python
from functools import wraps 
def timer(func):
    """A decorator that prints how long a function took to run."""

    @wraps(func)
    def wrapper(*args, **kwargs): 
        t_start = time.time()

        result = func(*args, **kwargs)
        t_total = time.time() - t_start
        print('{} took {}s'.format(func.__name__, t_total))
        
        return result
    return wrapper
```
Python provides us with an easy way to fix this. The wraps() function from the functools module is a decorator that you use when defining a decorator. If you use it to decorate the wrapper function that your decorator returns, it will modify wrapper()'s metadata to look like the function you are decorating.

If we use this updated version of the timer() decorator to decorate sleep_n_seconds() and then try to print sleep_n_seconds()'s docstring, we get the result we expect.


**Decorate print_sum() with the add_hello() decorator to replicate the issue that your friend saw - that the docstring disappears.**
```python
from functools import wraps

def add_hello(func):
  # Decorate wrapper() so that it keeps func()'s metadata
  @wraps(func)
  def wrapper(*args, **kwargs):
    """Print 'hello' and then call the decorated function."""
    print('Hello')
    return func(*args, **kwargs)
  return wrapper
  
@add_hello
def print_sum(a, b):
  """Adds two numbers and prints the sum"""
  print(a + b)
  
print_sum(10, 20)
print_sum_docstring = print_sum.__doc__
print(print_sum_docstring)

Hello
30
Adds two numbers and prints the sum
```

```python
@check_everything
def duplicate(my_list):
  """Return a new list that repeats the input twice"""
  return my_list + my_list

t_start = time.time()
duplicated_list = duplicate(list(range(50)))
t_end = time.time()
decorated_time = t_end - t_start

t_start = time.time()
# Call the original function instead of the decorated one
duplicated_list = duplicate.__wrapped__(list(range(50)))
t_end = time.time()
undecorated_time = t_end - t_start

print('Decorated time: {:.5f}s'.format(decorated_time))
print('Undecorated time: {:.5f}s'.format(undecorated_time))

Finished checking inputs
Finished checking outputs
Decorated time: 1.51298s
Undecorated time: 0.00007s
```

## 4.3. Decorators that take arguments

**run_n_times()**
```python
def run_n_times(func):
    def wrapper(*args, **kwargs):
    # How do we pass "n" into this function? 
    for i in range(???):
        func(*args, **kwargs) 
    return wrapper

@run_n_times(3)
def print_sum(a, b): 
    print(a + b)
@run_n_times(5) 
def print_hello():
    print('Hello!')

```

**A decorator factory**
```python
def run_n_times(n):
    """Define and return a decorator""" 
    def decorator(func):
        def wrapper(*args, **kwargs): 
            for i in range(n):
                func(*args, **kwargs) 
            return wrapper
        return decorator 

run_three_times = run_n_times(3) 
@run_three_times
def print_sum(a, b): 
    print(a + b)
@run_n_times(3)
def print_sum(a, b): 
    print(a + b)
```

```python
@run_n_times(3)
def print_sum(a, b): 
    print(a + b)
print_sum(3, 5)
```
<img src="/assets/images/20210426_FunctionsInPython/pic15.png" class="largepic"/>

```python
@run_n_times(5) 
def print_hello():
    print('Hello!') 
print_hello()
```
<img src="/assets/images/20210426_FunctionsInPython/pic16.png" class="largepic"/>

```python
# Modify the print() function to always run 20 times
print = run_n_times(20)(print)

print('What is happening?!?!')
```

```python
# Make hello() return bolded text
@html('<b>', '</b>')
def hello(name):
  return 'Hello {}!'.format(name)

print(hello('Alice'))

# Wrap the result of hello_goodbye() in <div> and </div>
@html('<b>', '</b>')
def hello_goodbye(name):
  return '\n{}\n{}\n'.format(hello(name), goodbye(name))
  
print(hello_goodbye('Alice'))
```

**Practice**
`**Run_n_times()**`

```python
def run_n_times(n):
  """Define and return a decorator"""
  def decorator(func):
    def wrapper(*args, **kwargs):
      for i in range(n):
        func(*args, **kwargs)
    return wrapper
  return decorator

# Use run_n_times() to create the run_five_times() decorator
run_five_times = run_n_times(5)

@run_five_times
def print_sum(a, b):
  print(a + b)
  
print_sum(4, 100)
```

```python
# Modify the print() function to always run 20 times
print = run_n_times(20)(print)

print('What is happening?!?!')

What is happening?!?!
What is happening?!?!
What is happening?!?!
...
```

**HTML Generator**
```python
def bold(func):
  @wraps(func)
  def wrapper(*args, **kwargs):
    msg = func(*args, **kwargs)
    return '<b>{}</b>'.format(msg)
  return wrapper
def italics(func):
  @wraps(func)
  def wrapper(*args, **kwargs):
    msg = func(*args, **kwargs)
    return '<i>{}</i>'.format(msg)
  return wrapper

def html(open_tag, close_tag):
  def decorator(func):
    @wraps(func)
    def wrapper(*args, **kwargs):
      msg = func(*args, **kwargs)
      return '{}{}{}'.format(open_tag, msg, close_tag)
    # Return the decorated function
    return wrapper
  # Return the decorator
  return decorator

# Make hello() return bolded text
@html('<b>', '</b>')
def hello(name):
  return 'Hello {}!'.format(name)

print(hello('Alice'))

<b>Hello Alice!</b>

# Make goodbye() return italicized text
@html('<i>', '</i>')
def goodbye(name):
  return 'Goodbye {}.'.format(name)
  
print(goodbye('Alice'))

<i>Goodbye Alice.</i>

# Wrap the result of hello_goodbye() in <div> and </div>
@html('<div>', '</div>')
def hello_goodbye(name):
  return '\n{}\n{}\n'.format(hello(name), goodbye(name))
  
print(hello_goodbye('Alice'))

<div>
<b>Hello Alice!</b>
<i>Goodbye Alice.</i>
</div>

```


## 4.4. Timeout(): a real world example

**Time out**

```python
@timeout
def function1():
# This function sometimes # runs for a loooong time
...
@timeout
def function2():
# This function sometimes # hangs and doesn't return
...
```

**Time out background info**
```python
import signal
def raise_timeout(*args, **kwargs): 
    raise TimeoutError()
# When an "alarm" signal goes off, call raise_timeout() 
signal.signal(signalnum=signal.SIGALRM, handler=raise_timeout) 
# Set off an alarm in 5 seconds
signal.alarm(5)
# Cancel the alarm 
signal.alarm(0)
```

```python
def timeout_in_5s(func): 
    @wraps(func)
    def wrapper(*args, **kwargs):
         # Set an alarm for 5 seconds 
         signal.alarm(5)
    try:
        # Call the decorated func 
        return func(*args, **kwargs)
    finally:
        # Cancel alarm 
        signal.alarm(0)
    return wrapper
```
```python
@timeout_in_5s 
def foo():
    time.sleep(10) 
    print('foo!')

foo()
```
<img src="/assets/images/20210426_FunctionsInPython/pic17.png" class="largepic"/>

```python
def timeout(n_seconds):
    def decorator(func): 
        @wraps(func)
        def wrapper(*args, **kwargs): 
            # Set an alarm for n seconds
            signal.alarm(n_seconds) 
            try:
                # Call the decorated func 
                return func(*args, **kwargs)
            finally:
                # Cancel alarm 
                signal.alarm(0)
return wrapper return decorator
```

```python
@timeout(5) 
def foo():
    time.sleep(10) 
    print('foo!')
@timeout(20) 
def bar():
    time.sleep(10) 
    print('bar!')
foo()
```
<img src="/assets/images/20210426_FunctionsInPython/pic17.png" class="largepic"/>

```python
bar()
```
<img src="/assets/images/20210426_FunctionsInPython/pic18.png" class="largepic"/>

**Finally**
**Tag your function**
```python
def tag(*tags):
  # Define a new decorator, named "decorator", to return
  def decorator(func):
    # Ensure the decorated function keeps its metadata
    @wraps(func)
    def wrapper(*args, **kwargs):
      # Call the function being decorated and return the result
      return func(*args, **kwargs)
    wrapper.tags = tags
    return wrapper
  # Return the new decorator
  return decorator

@tag('test', 'this is a tag')
def foo():
  pass

print(foo.tags)

('test', 'this is a tag')
```

**Check the return type**
```python
def returns(return_type):
  # Complete the returns() decorator
  def decorator(func):
    def wrapper(*args, **kwargs):
      result = func(*args, **kwargs)
      assert(type(result) == return_type)
      return result
    return wrapper
  return decorator
  
@returns(dict)
def foo(value):
  return value

try:
  print(foo([1,2,3]))
except AssertionError:
  print('foo() did not return a dict!')


<script.py> output:
    foo() did not return a dict!
```

# 5. Reference

1. [Writing Functions in Python - DataCamp](https://learn.datacamp.com/courses/writing-functions-in-python)

