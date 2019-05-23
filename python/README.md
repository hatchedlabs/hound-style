# Python

We (also because of Hound) use [Flake8](http://flake8.pycqa.org/en/latest/) to monitor Python code style and sometimes code quality.

A list of complete rules can be found at [https://lintlyci.github.io/Flake8Rules/](https://lintlyci.github.io/Flake8Rules/).

Flake8 also supports [extensions](https://github.com/DmytroLitvinov/awesome-flake8-extensions) which can help adding more constraints or verifying more code issues. Unfortunately, Hound doesn't allow installing anything we'd like to but we can compensate installing the extensions locally.

A few recommended extensions (already enabled in [.flake8](.flake8) config file):

```
# Q0 - always expect single quotes
pip install flake8-quotes

# B9 - find likely bugs and design problems in your program
pip install flake8-bugbear

# I - checks the ordering of imports: from Python core to local
pip install flake8-import-order

# no "dead" code
pip install flake8-eradicate

# better comprehensions
pip install flake8-comprehensions
```

It's highly recommended to install the extensions and lint the code locally as part of each commit (see the "Local setup" section). This would ensure a common way of writing and reading code in the organisation.

## Local setup

Clone the [Hatched style repo](https://github.com/hatchedlabs/hound-style) on your machine, maybe on the same level as other Hatched projects. The path would be important for reference later.

Each Python file can be checked locally, before being pushed to Github by using Git hooks and a library like [Overcommit](https://github.com/sds/overcommit). Git hooks will ensure that problems are caught before Hound gets to analyse code and the development cycle gets faster.

Example Python config for ".overcommit.yml" in the root of [grocery-data](https://github.com/hatchedlabs/grocery-data) project:

```
PreCommit:
  ALL:
    problem_on_unmodified_line: ignore

  PythonFlake8:
    enabled: true
    flags: ['--config=../hound-style/python/.flake8']
```

### Sublime

Install [SublimeLinter-flake8](https://github.com/SublimeLinter/SublimeLinter-flake8) package, an extension for SublimeLinter.

Menu: Preferences > Package Settings > SublimeLinter > Settings

Example config (path specific to local machine):

```
// SublimeLinter Settings - User
{
  "linters": {
    "flake8": {
      "args": "--config=~/dev/hatchedlabs/hound-style/python/.flake8"
    }
    ...
  },
  ...
}
```

## Rules

### E221, E222 whitespace around operator

PEP8 [recommends](https://www.python.org/dev/peps/pep-0008/#other-recommendations) having a single whitespace around operators. We allow multiple whitespaces around operators, but the flexibility is for `=` only.

**Why?**

In practice, we might want to align variable assignment for readability.

```
# accepted
variable                       = 'one'
longer_variable_name           = 'two'
the_longest_variable_name_here = 'three'

# bad - programmer's responsibility
x =   4  *     5

# good
x = 4 * 5
```

### 
### E265, E266 block comment should start with '# ' and only one '#'

Part of [PEP8](https://www.python.org/dev/peps/pep-0008/#block-comments):

> Each line of a block comment starts with a # and a single space (unless it is indented text inside the comment).

**Why?**

Good because if we want to allow more than one _#_, then how do we make a difference between 3, 4 or more or less? Things will look messy and too dev specific.

### E302, E305 blank lines

Part of [PEP8](https://www.python.org/dev/peps/pep-0008/#blank-lines):

> Surround top-level function and class definitions with two blank lines.

**Why?**

There are no block endings in Python, it's all based on indentation so spacing helps visibility and reading code. It allows easily reading and separating logical blocks inside a function from different functions in the same file.

```
# bad
def one_function():
    var1 = 'smth'
    var2 = call_some_function()
    var3 = 123
    call_something_else()
    # some comments about what we expect from do_some_operations()
    res = do_some_operations()
    return parse_the_result_of_oeperations(res)
def another_function(arg1, arg2):
    var1 = 'smth'
    some_other_logic()
    and_yet_another_function()


# good


def one_function():
    var1 = 'smth'
    var2 = call_some_function()
    var3 = 123

    call_something_else()

    # some comments about what we expect from do_some_operations()
    res = do_some_operations()

    return parse_the_result_of_oeperations(res)


def another_function(arg1, arg2):
    var1 = 'smth'
    some_other_logic()
    and_yet_another_function()
```

### E402 module level imports

Part of [PEP8](https://www.python.org/dev/peps/pep-0008/#imports):

> Imports are always put at the top of the file, just after any module comments and docstrings, and before module globals and constants.

**Why?**

Dependencies are declared in one place and it keeps a module or class organized. When reading the file it's easy to understand what to expect from it and which are the other libraries or modules it relies on. It's also a commond best practice with other programming languages.

**Exception**

If however an import is justified to not be at the beginning of the file, for example a conditional import, the rule can be specifically disabled for that particular exception:

```
# accepted in anywhere with very good reason
import sys  # noqa: E402
```

### E501 line length

[PEP8](https://www.python.org/dev/peps/pep-0008/#maximum-line-length) recommends 79 & 72 characters for a line length:

>Limit all lines to a maximum of 79 characters.
>
>For flowing long blocks of text with fewer structural restrictions (docstrings or comments), the line length should be limited to 72 characters.

However, in practice, this can be too short, especially when variables and functions have good, descriptive names. [PEP8](https://www.python.org/dev/peps/pep-0008/#maximum-line-length) also accepts 99 & 72 characters but our current setting is **100** chars for a line of code.

**Why?**

No need to scroll the page horizontally when reading 2 files side by side. This helps when writing tests in one file and the code in another but also when fixing Git conflicts. Longer than 100 characters makes comparing files more difficult.

PR review is more difficult when you need to scroll horizontally to read a file, you can loose context.

Word wrapping is confusing for code and makes the context difficult to understand. Specially for Python where block separation relies on indentation.

## Exceptions

Sometimes, we have really good reasons for ignoring a certain rule. Instead of disabling the rule completely, we can just [disable](https://flake8.pycqa.org/en/3.7.7/user/violations.html?highlight=ignore#in-line-ignoring-errors) it specifically when we want to:

```
# ignore both E402 and I100 for this line
import sys  # noqa: E402,I100
```
