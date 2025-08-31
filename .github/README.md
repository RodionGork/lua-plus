# Lua-plus

This is the clone of the [official **Lua** repo](https://github.com/lua/lua), with an attempt to make small additions to
make the language bit more convenient to programmers with experience from other languages. You can try them in the
[online sandbox](https://rodiongork.github.io/lua-emcc) and/or see example with
[drawing on canvas](https://rodiongork.github.io/lua-emcc/example5.html).

It is 100% compatible with the original Lua - e.g. any amends only extend functionality but don't break or change
existing behavior.

By now, at initial attempt only two small amends are made:

1. **"Not equals" operator** could be spelled as `!=` in addition to standard `~=`, and also unary `!` could be used as
    logical `not`.

2. **Compount assignments** added (e.g. `+=`, `-=`, `*=` and so on, including `..=` for concatenating assignment)

3. **Reserved word "function"** could be shortened to `fn` to help declaring lambdas etc.

4. **Type-hint comments** could be used as short identifiers following the
arguments of a function, or the function header itself, separated with `:` - they are not checked (i.e. serve exactly as a comment of special form) but some
external tool could be introduced to verify them later.

**Example**

```lua
fn sqr(x:number):number    -- short 'function' alias and typehint comments
  return x*x
end

res = ''
sum = 0
for i = 1, 20 do
  sum += sqr(i)            -- compound assignment (for addition)
  print(sum)
  if i % 3 != 0 then       -- not-equals alias
    res ..= 'fuzz' 
  end
  if !(i % 5 == 0) then    -- logical-not alias
    res ..= 'bizz'
  end
  res ..= ' '              -- compound assignment (for concatenation)
end

print(res)
```

**Note** that all those changes are made on the parser level, so they (should) generate
identical opcodes (to standard not-equals `~=` and simple assignment) `a=a+x`.

Particularly compound assignment is just "expanded" to normal one, e.g. when parser meets `a+=x`, it is transformed in memory to tokens `a=a+x` to ensure that generated code is identical. Minor
exception is that priority of the compound operator is very low (so that `a *= 2 + 1` becomes
`a = a * (2 + 1)` rather than `a = a * 2 + 1`.

This may have curious consequences in case when expressions with sideffects are used, for example:

```lua
x = 0

function test()
  x = x+1
  return x
end

a = {1, 4, 9}

a[test()] += 10

print(table.concat(a, ' '))
```

However we think this is a reasonable trade-off and anyway normally people avoid writing such a queer code
(as the order of function calls may be confusing anyway).

Another interesting effect is that compound assignments do not use new type of token for operator, it just
is built of two separate operators - hence they could be separated with space: `a +   = b`. It's just important
that if statement starts with l-value and then one of binary operators suitable for compound assignment follows -
then we expect to meet assignment symbol next.
