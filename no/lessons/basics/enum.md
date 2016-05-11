---
layout: page
title: Enum
category: basics
order: 3
lang: no
---

Et sett med algoritmer for å iterere over kolleksjoner.

{% include toc.html %}

## Enum

Modulen `Enum` inneholder over hundre funksjoner som vi kan benytte av oss når vi jobber med kolleksjoner.

Denne leksjonen vil kun dekke en brøkdel av de tilgjengelige funksjonene. For en komplett liste av alle funksjonene, se den offisielle  [`Enum`](http://elixir-lang.org/docs/v1.0/elixir/Enum.html) dokumentasjonen, eller [`Stream`](http://elixir-lang.org/docs/v1.0/elixir/Stream.html) for lazy enums.


### all?

Ved bruk av `all?` (og de fleste andre av Enums funksjoner), forsyner vi kolleksjonens elementer med en funksjon. I `all?` sitt tilfelle, må hele kolleksjonen evalueres til `true`, ellers vil `false` bli returnert:


```elixir
iex> Enum.all?(["foo", "bar", "hello"], fn(s) -> String.length(s) == 3 end)
false
iex> Enum.all?(["foo", "bar", "hello"], fn(s) -> String.length(s) > 1 end)
true
```

### any?

I motsetning til eksemplet over, vil `any?` returnere `true` hvis minst et av elementene evalueres til `true`:

```elixir
iex> Enum.any?(["foo", "bar", "hello"], fn(s) -> String.length(s) == 5 end)
true
```

### chunk

Hvis vi trenger å dele opp kolleksjonen i mindre deler, kan vi benytte oss av funksjonen `chunk`:

```elixir
iex> Enum.chunk([1, 2, 3, 4, 5, 6], 2)
[[1, 2], [3, 4], [5, 6]]
```

Det finnes flere alternativer for `chunk`, men vi blir ikke å gå igjennom dem. Se  [`chunk/2`](http://elixir-lang.org/docs/v1.0/elixir/Enum.html#chunk/2) i den offisielle dokumentasjonen for å lære mer.

### chunk_by

Hvis vi trenger å gruppere kolleksjonen vår basert på noe annet enn størrelse, kan vi bruke funksjonen `chunk_by`:

```elixir
iex> Enum.chunk_by(["one", "two", "three", "four", "five"], fn(x) -> String.length(x) end)
[["one", "two"], ["three"], ["four", "five"]]
```

### each

Det kan bli nøvendig å måtte iterere over en kolleksjon uten å produsere en verdi. Vi kan da bruke funksjonen `each`:

```elixir
iex> Enum.each(["one", "two", "three"], fn(s) -> IO.puts(s) end)
one
two
three
```

__Merk__: Funksjonen `each` returnerer atomet `:ok`.

### map

For å forsyne funksjonen vår til hvert element, og produsere en ny kolleksjon kan vi benytte oss av `map`:

```elixir
iex> Enum.map([0, 1, 2, 3], fn(x) -> x - 1 end)
[-1, 0, 1, 2]
```

### min

For å finne den minste verdien i kolleksjonen vår kan vi bruke `min`:

```elixir
iex> Enum.min([5, 3, 0, -1])
-1
```

### max

`max` returnerer den største verdien i kolleksjonen:

```elixir
iex> Enum.max([5, 3, 0, -1])
5
```

### reduce

Vi kan vanne ut kolleksjonen vår ned til kun et enkelt element ved å bruke `reduce`. Vi kan tilføye en valgfri akkumulator (`10` i eksemplet under) til funksjonen vår. Hvis ingen akkumulator er gitt, vil det første elementet bli brukt:

```elixir
iex> Enum.reduce([1, 2, 3], 10, fn(x, acc) -> x + acc end)
16
iex> Enum.reduce([1, 2, 3], fn(x, acc) -> x + acc end)
6
```

### sort

Sortering av en kolleksjon er enkelt ved hjelp av to `sort` funksjoner. Det første alternativet bruker Elixirs eget begrep for sortering til å avgjøre sorteringsrekkefølgen:

```elixir
iex> Enum.sort([5, 6, 1, 3, -1, 4])
[-1, 1, 3, 4, 5, 6]

iex> Enum.sort([:foo, "bar", Enum, -1, 4])
[-1, 4, Enum, :foo, "bar"]
```

Den andre alternativet lar oss velge sorteringsrekkefølgen selv:

```elixir
# Med vår egen funksjon
iex> Enum.sort([%{:val => 4}, %{:val => 1}], fn(x, y) -> x[:val] > y[:val] end)
[%{val: 4}, %{val: 1}]

# uten vår egen funksjon
iex> Enum.sort([%{:count => 4}, %{:count => 1}])
[%{count: 1}, %{count: 4}]
```

### uniq

Vi kan benytte oss av `uniq` for å fjerne duplikater fra kolleksjonen vår:

```elixir
iex> Enum.uniq([1, 2, 2, 3, 3, 3, 4, 4, 4, 4])
[1, 2, 3, 4]
```

