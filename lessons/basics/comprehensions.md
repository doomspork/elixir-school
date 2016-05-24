---
layout: page
title: Comprehensions
category: basics
order: 13
lang: en
---

List comprehensions are syntactic sugar for looping through enumerables in Elixir.  In this lesson we'll look at how we can use comprehensions for iteration and generation.

{% include toc.html %}

## Basics

Often times comprehensions can be used to produce more concise statements for `Enum` and `Stream` iteration.  Let's start by looking at a simple comprehension and then break it down:

```elixir
iex> list = [1, 2, 3, 4, 5]
iex> for x <- list, do: x*x
[1, 4, 9, 16, 25]
```

The first thing we notice is the use of `for` and a generator.  What is a generator?  Generators are the `x <- [1, 2, 3, 4]` expressions found in list comprehensions, they're responsible for generating the next value.

Lucky for us comprehensions aren't limited to lists, in fact they'll work with any enumerable:

```elixir
# Keyword Lists
iex> for {_key, val} <- [one: 1, two: 2, three: 3], do: val
[1, 2, 3]

# Maps
iex> for {k, v} <- %{"a" => "A", "b" => "B"}, do: {k, v}
[{"a", "A"}, {"b", "B"}]

# Binaries
iex> for <<c <- "hello">>, do: <<c>>
["h", "e", "l", "l", "o"]
```

As you may have noticed, generators rely on pattern matching to compare their input set to the left side variable.  In the event a match is not found, the value is ignored:

```elixir
iex> for {:ok, val} <- [ok: "Hello", error: "Unknown", ok: "World"], do: val
["Hello", "World"]
```

It's possible to use multiple generators, much like nested loops:

```elixir
iex> list = [1, 2, 3, 4]
iex> for n <- list, times <- 1..n do
...>   String.duplicate("*", times)
...> end
["*", "*", "**", "*", "**", "***", "*", "**", "***", "****"]
```

To better illustrate the looping that is occurring, let's use `IO.puts` to display the two generated values:

```elixir
iex> for n <- list, times <- 1..n, do: IO.puts "#{n} - #{times}"
1 - 1
2 - 1
2 - 2
3 - 1
3 - 2
3 - 3
4 - 1
4 - 2
4 - 3
4 - 4
```

List comprehensions are syntactic sugar and should be used only when appropriate.

## Filters

You can think of filters as a sort of guard for comprehensions.  When a filtered value returns `false` or `nil` it is excluded from the final list.  Let's loop over a range and only worry about even numbers:

```elixir
iex> for x <- 1..10, rem(x, 2) == 0, do: x
[2, 4, 6, 8, 10]
```

Like generators, we can use multiple filters.  Let's example our range and filter out values that aren't even and cannot be divided evenly by 3:

```elixir
iex> for x <- 1..100,
...>   rem(x, 2) == 0,
...>   rem(x, 3) == 0, do: x
[6, 12, 18, 24, 30, 36, 42, 48, 54, 60, 66, 72, 78, 84, 90, 96]
```

## Using `:into`

What if we want to produce something other than a list?  Given the `:into` option we can do just that!  As a general rule of thumb, `:into` accepts any structure that implements the `Collectable` protocol.

Using `:into`, let's create a map from a keyword list:

```elixir
iex> for {k, v} <- [one: 1, two: 2, three: 3], into: %{}, do: {k, v}
%{one: 1, three: 3, two: 2}
```

Since bitstrings are enumerable we can use list comprehensions and `:into` to create strings:

```elixir
iex> for c <- [72, 101, 108, 108, 111], into: "", do: <<c>>
"Hello"
```

That's it!  List comprehensions are an easy way to iterate through collections in a concise manner.
