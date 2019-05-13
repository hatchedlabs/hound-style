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

# No "dead" code
pip install flake8-eradicate
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
    call_somethin_else()
    # some comments about what we expect from do_some_operations()
    res = do_some_operations()
    return is_jon_snow_going_to_be_king_of_the_seven_kingdoms(res)
def another_function(arg1,be_king):
    var1 = 'smth'
    some_other_logic()
    sopranos_is_still_the_best()


# good


def one_function():
    var1 = 'smth'
    var2 = call_some_function()
    var3 = 123

    call_somethin_else()

    # some comments about what we expect from do_some_operations()
    res = do_some_operations()

    return is_jon_snow_going_to_be_king_of_the_seven_kingdoms(res)


def another_function(arg1,be_king):
    var1 = 'smth'
    some_other_logic()
    sopranos_is_still_the_best()
```

### E501 Line length

[PEP8](https://www.python.org/dev/peps/pep-0008/#maximum-line-length) recommends 79 & 72 characters for a line length:

>Limit all lines to a maximum of 79 characters.
>
>For flowing long blocks of text with fewer structural restrictions (docstrings or comments), the line length should be limited to 72 characters.

However, in practice, this can be too short, especially when variables and functions have good, descriptive names. [PEP8](https://www.python.org/dev/peps/pep-0008/#maximum-line-length) also accepts 99 & 72 characters but our current setting is **100** chars for a line of code.

**Why?**

No need to scroll the page horizontally when reading 2 files side by side. This helps when writing tests in one file and the code in another but also when fixing Git conflicts. Longer than 100 characters makes comparing files more difficult.

PR review is more difficult when you need to scroll horizontally to read a file.

Word wrapping is confusing for code and makes the context difficult to understand. Specially for Python where block separation relies on indentation.


