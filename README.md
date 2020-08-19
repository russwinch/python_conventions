# Python conventions

This is my preferred style when writing Python code. 

This advice follows PEP-8 as closely, with some personal preference mixed in when there is room for interpretation.
As with any conventions, they should be followed consistently but not to the point of impacting readability of code.
*sometimes you just need a few characters extra over the character limit...*

## Contents

- [Spaces over tabs](#spaces-over-tabs)
- [Character limit](#character-limit)
- [Multiline layouts](#multiline-layouts)
- [Binary operators](#binary-operators)
- [String quotes](#string-quotes)
- [String formatting](#string-formatting)
- [Docstrings](#docstrings)
- [Testing](#testing)

### Spaces over tabs

Spaces are preferred, as [recommended in PEP-8](https://www.python.org/dev/peps/pep-0008/#tabs-or-spaces)

### Character limit

88 characters as per [Black](https://github.com/psf/black#line-length).
This seems to be a reasonable limit to adhere to and displays well on most platforms, even with monitors that are 
in portrait orientations and split. Occasional exceptions to this rule are of course fine for readability, but a hard limit of 100-120 is sensible.

### Multiline layouts

There are a number of [options for multiline layouts](https://www.python.org/dev/peps/pep-0008/#code-lay-out) in PEP-8.
Vertical alignment is the preferred style, defaulting to a single argument per line for readability, except when there are small numbers
of arguments or when they share an obvious association:
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
argument to avoid sad faces in the code (this one of the reasons I don't use [Black](https://black.readthedocs.io/en/stable/)):
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
If reducing git diffs is important, always use the multi-line version, with commas after each line:
```
def long_function_name(
  var_one,
  var_two,
  var_three,
):
  
somevar = [
  'things',
  'stuff',
  'others',
]
```

When using . syntax hide your train-wreck code (ie SQLAlchemy queries) by using parenthesis and breaking on the . rather than using
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

### Binary operators

It's permissable in PEP-8 to break [before or after](https://www.python.org/dev/peps/pep-0008/#should-a-line-break-before-or-after-a-binary-operator) binary operators but Knuth's style of breaking before the operator is preferable for readability:
```
total = (first_sold_item
         + second_sold_item
         - bought_item)

if (really_long_variable
    == other_really_long_variable):
    ...
```

### String quotes

Single and double quotes are the same in Python [PEP8 leaves this open to interpretation](https://www.python.org/dev/peps/pep-0008/#string-quotes).
Use single quotes for short strings and double quotes for longer, human readable strings, ie print statements, logging etc. Single quotes are easier on the eyes than double (this is the other reason I don't use Black).
```
color = `red`
print("My favourite color is red")
```
*sub note: as above, prefer the American spelling for variable names as this is more internationally accepted by developers
with English as a second language...unless they are particularly painful to use eg 'specialization'*

### String formatting

F-strings, introduced in Python 3.6 are generally the neatest way to insert data into strings:
```
animals = ['dogs', 'cats']
for a in animals:
  print(f"I like {a}")
  
print(f"1 plus 1 is {1 + 1}")
```

The exception is some kind of template that needs to be created reused multiple times in different places in the code,
here the ```.format``` method should to be used:
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

Docstrings should be applied to all modules, classes, methods and functions to aid anyone using them in future.
As per [PEP-257](https://www.python.org/dev/peps/pep-0257/) Docstrings are contained within triple double quotes """.

Prefer the [Google style of docstrings](http://google.github.io/styleguide/pyguide.html#381-docstrings):
```
def my_func(arg_a, arg_b):
"""Describe the purpose of the function here.

Args:
  arg_a(str): what arg_a is
  arg_b(int): what arg_b is
  
Returns:
  tuple: what the returned item is  # this is the modification over strict Google style
  
Raises:
  ValueError: reason for this error
"""
```
Additional text can be added, but this should be the minimum for all but the simplest functions, ie obvious one liners where
the one liner version of the docstring can be used:
```
def update_user(data):
"""Updates the user with new data."""
    User.update(data)
```

### Testing

Use Pytest over unittest, ```assert``` is much simpler and fixtures are powerful.

Group tests using classes and methods and use descriptive names for the methods to help when the test fails:
```
class TestSomeFunctionality:
  def test_zero_is_returned_when_no_values_are_supplied(self):
    ...
    
  def test_something_else_descriptive(self):
    ...
```
*Dont forget to add ```self``` to each method when using classes to organise tests; the error message is very cryptic when also using fixtures*

Each unit test should be organised into three blocks, separated by a new line:

1. Arrange
2. Act
3. Assert

Put the expected result on the end of the *Act* block as this is best for readability.

For consistency `result` and `expected_result` should be used as variable names, where required:
```
def test_something_works():
  some_data = ['a', 'b', 'c']
  other_date = 0
  
  result = func_under_test(some_data, other_data)
  
  expected_result = ['cat', 'dog', 'frog']
  assert result == expected_result
```
