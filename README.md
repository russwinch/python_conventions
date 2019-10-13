# Python conventions

This is my preferred style when writing Python code. 

This advice follows PEP-8 as closely, with some personal preference mixed in when there is room for interpretation.
As with any conventions, they should be followed consistently but not to the point of impacting readability of code...
...sometimes you just need a few characters extra over the character limit...

## Contents

- [Spaces over tabs](https://github.com/russwinch/python_conventions/blob/master/README.md#spaces-over-tabs)
- [Character limit](https://github.com/russwinch/python_conventions/blob/master/README.md#character-limit)
- [Multiline layouts](https://github.com/russwinch/python_conventions/blob/master/README.md#multiline-layouts)
- [String quotes](https://github.com/russwinch/python_conventions/blob/master/README.md#string-quotes)
- [String formatting](https://github.com/russwinch/python_conventions/blob/master/README.md#string-formatting)
- [Docstrings](https://github.com/russwinch/python_conventions/blob/master/README.md#docstrings)
- [Testing](https://github.com/russwinch/python_conventions/blob/master/README.md#testing)

### Spaces over tabs

Spaces are preferred, as recommended in [PEP-8](https://www.python.org/dev/peps/pep-0008/#tabs-or-spaces)

### Character limit

88 characters as per [Black](https://github.com/psf/black#line-length).
This seems to be a reasonable limit to adhere to and displays well on most platforms, even with monitors that are 
in portrait orientations and split. Occasional exceptions are fine for readability but don't go overboard!

### Multiline layouts

[There are a number of options](https://www.python.org/dev/peps/pep-0008/#code-lay-out) for multiline layouts in PEP-8
Vertical alignment is the preferred style, defaulting to a single argument per line for readability, except when there are small numbers
of arguments or when they share an association:
```
# Aligned with opening delimiter.
foo = long_function_name(var_one,
                         var_two,
                         var_three,
                         var_four)
                         
# Args associated with one another
foo = long_function_name(first_name, last_name,
                         address_one, address_two, address_three)
```
If there is no option other than to use the trailing indent then keep the closing parenthesis on the same line as the last
argument to avoid sad faces into the code (this one of the reasons I don't use Black):

```
# No:
def long_function_name(
  var_one,
  var_two,
  var_three
  ):  # why so sad?

# Yes:
def long_function_name(
  var_one,
  var_two,
  var_three):
```

When using . syntax avoid train-wreck code (ie SQLAlchemy queries) by using parenthesis and breaking on the . rather than using
backslashes:
```
# No:
user = session.query(User.id, User.name, User.address).filter(User.name == user_name).order_by(User.name).all()

# No:
user = session.query(User.id, User.name, User.address) \
       .filter(User.name == user_name).order_by(User.name).all()

# Yes:
user = (session
        .query(User.id, User.name, User.address)
        .filter(User.name == user_name)
        .order_by(User.name)
        .all())
```

### String quotes

As single and double quotes are the same [PEP8 leaves this open to interpretation](https://www.python.org/dev/peps/pep-0008/#string-quotes).
Use single quotes for short strings and double quotes for longer, human readable strings, ie print statements, logging etc. Single quotes are easier on the eyes than double (this is the other reason I don't use Black).
```
color = `red`
print("My favourite color is red")
```
*sub note: as above, prefer the American spelling for variable names as this is more internationally accepted by developers
with English as a second language...unless they are particularly painful to use eg 'specialization'*

### String formatting

F-strings, introduced in Python 3.6 are genreally the neatest way to insert data into strings:
```
animals = ['dogs', 'cats']
for a in animals:
  print(f"I like {a}")
  
print(f"1 plus 1 is {1 + 1}")
```

The exception is some kind of template needs to be created reused multiple times in different places in the code
and then the ```.format``` method should to be used:
```
# bad example hopefully useful
query = "I would like to find out about {}"

if some_thing:
  value = get_value():
  query.format(value)
else:
  other_value = get_other_value()
  query.format(other_value)
```

### Docstrings

Docstrings should be applied to all modules, classes, methods and functions to aid those using them in future.
They should be contained within triple double quotes """.

Perfer the [Google style of docstrings](http://google.github.io/styleguide/pyguide.html#381-docstrings)
with a subtle modification to the ```Returns / Yields``` section to show the type in brackets for consistency 
with those in the Args section:
```
def my_func(arg_a, arg_b):
"""Describe the purpose of the function here.

Args:
  arg_a(str): what arg_a is
  arg_b(int): what arg_b is
  
Returns:
  (tuple): what the returned item is
"""
```
Additional text can be added, but this should be the minumum for all but the simplest functions, ie obvious one liners where
the one liner version of the docstring can be used:
```
def update_user(data):
"""Updates the user with new data."""
    User.update(data)
```

### Testing

Use Pytest over unittest, ```assert``` is much simpler and fixtures are very powerful.

Unittests should be organised into three blocks, separated by a new line:

1. arrange
2. act
3. assert

I put the expected result on the end of the act as I find this is best for readability.

For consistency use `result` and `expected_result` as variable names:
```
def test_something_works():
  some_data = ['a', 'b', 'c']
  other_date = 0
  
  result = func_under_test(some_data, other_data)
  expected_result = ['cat', 'dog', 'frog']
  
  assert result == expected_result
```

Group tests using classes and methods and use descriptive names for the methods to help when the test fails:
```
class TestSomeFunctionality:
  def test_zero_is_returned_when_no_values_are_supplied(self):
    ...
    
  def test_something_else_descriptive(self):
    ...
```
**Dont forget to add 'self' to the methods, the error message can be cryptic sometimes, particularly in fixtures**
