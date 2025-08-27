# Lua-plus

_**Note: this attempt tried to manipulate parsing without lookback but only
works for simple variables**_

This is the clone of the [official **Lua** repo](https://github.com/lua/lua), with an attempt to make small additions to
make the language bit more convenient to programmers with experience from other languages.

It is 100% compatible with the original Lua - e.g. any amends only extend functionality but don't break or change
existing behavior.

By now, at initial attempt only two small amends are made:

1. "Not equals" operator could be spelled as `!=` in addition to standard `~=`, and also unary `!` could be used as
    logical `not`.

2. Compount assignments (e.g. `+=`, `-=`, `*=` and so on, including `..=` for concatenating assignment) - they by now
    only will work with simple variables.

The research is ongoing to extend compound assignments on "lvalues" containing array cells etc. 
