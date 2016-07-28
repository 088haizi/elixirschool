---
layout: page
title: Колекции
category: basics
order: 2
lang: bg
---

Списъци, кортежи, ключови думи, асоциативни списъци и функционални комбинатори.

{% include toc.html %}

## Списъци

Списъците са прости колекции от стойности, могат да се състоят от множество типове; списъците може да съдържат еднакви стойности:

```elixir
iex> [3.14, :pie, "Apple"]
[3.14, :pie, "Apple"]
```

Elixir имплементира списъка като свързан списък.  Това означава, че дължината на списъка е операция от реда `O(n)`.  Поради тази причина по-принцип е по-бързо да се добавя в началото отколкото в края на списък:

```elixir
iex> list = [3.14, :pie, "Apple"]
[3.14, :pie, "Apple"]
iex> ["π"] ++ list
["π", 3.14, :pie, "Apple"]
iex> list ++ ["Cherry"]
[3.14, :pie, "Apple", "Cherry"]
```


### Конкатенация на списъци

Конкатенацията на списъци използва оператора `++/2`:

```elixir
iex> [1, 2] ++ [3, 4, 1]
[1, 2, 3, 4, 1]
```

### Разлика на списъци

Поддръжката за субстракция е осигурено чрез оператора `--/2`; безопасно е да се извади липсваща стойност:

```elixir
iex> ["foo", :bar, 42] -- [42, "bar"]
["foo", :bar]
```

**Забележка:** Използва се [стриктно сравнение](../basics/#comparison) при сравнението на стойности.

### Начало / Край

Когато използваме списъци е обичайно да работим с началото и края на списъка.  Началото е първият елемент на списъка, а краят останалите елементи.  Elixir предоставя два полезни метода, `hd` и `tl`, за работа с тези части:

```elixir
iex> hd [3.14, :pie, "Apple"]
3.14
iex> tl [3.14, :pie, "Apple"]
[:pie, "Apple"]
```

В допълнение към гореспоменатите функции, може да ползвате и оператора за пренасочване `|`; ще разгледаме този подход в следващи уроци:

```elixir
iex> [h|t] = [3.14, :pie, "Apple"]
[3.14, :pie, "Apple"]
iex> h
3.14
iex> t
[:pie, "Apple"]
```

## Кортежи

Кортежите са подобни на списъци, но се съхраняват последователно в паметта.  Поради това достъпваме тяхната дължина бързо, но модификациите са скъпи; новият кортеж трябва да бъде изцяло копиран наново в паметта.  Кортежите се дефинират с къдрави скоби:

```elixir
iex> {3.14, :pie, "Apple"}
{3.14, :pie, "Apple"}
```

Обичайно за кортежите е да бъдат използвани като механизъм за връщане на допълнителна информация от функции; полезността от това ще стане по-ясна след като навлезем в съвпадането на шаблони:

```elixir
iex> File.read("path/to/existing/file")
{:ok, "... contents ..."}
iex> File.read("path/to/unknown/file")
{:error, :enoent}
```

## Списъци с ключови думи

Ключовите думи и асоциативните списъци са асоциативните колекции на Elixir.  В Elixir, списък от ключови думи е специален списък от кортежи, чиито първи елемент е атом; те имат производителността на списък:

```elixir
iex> [foo: "bar", hello: "world"]
[foo: "bar", hello: "world"]
iex> [{:foo, "bar"}, {:hello, "world"}]
[foo: "bar", hello: "world"]
```

Трите характеристики на списъци с ключови думи подчертават тяхната важност:

+ Ключовете са атоми.
+ Ключовете са подредени.
+ Ключовете не са уникални.

Поради тези причини списъците с ключови думи най-често се ползват за предаване на опции към функции.

## Асоциативни списъци

В Elixir асоциативните списъци съхраняват стойности тип ключ-стойност "по подразбиране", за разлика от списъци с ключови думи, те позволяват ключове от всякакъв тип и не следват подреждане.  Може да дефинирате асоциативен списък със синтаксис `%{}`:

```elixir
iex> map = %{:foo => "bar", "hello" => :world}
%{:foo => "bar", "hello" => :world}
iex> map[:foo]
"bar"
iex> map["hello"]
:world
```

Считано от  Elixir 1.2 променливи са допустими като ключове в асоциативния списък:

```elixir
iex> key = "hello"
"hello"
iex> %{key => "world"}
%{"hello" => "world"}
```

Ако дубликат е добавен към асоциативен списък, той ще замени предходната стойност:

```elixir
iex> %{:foo => "bar", :foo => "hello world"}
%{foo: "hello world"}
```

Както може да видим от резултата по-горе, съществува специален синтаксис за асоциативни списъци, които съдържат само атоми като ключове:

```elixir
iex> %{foo: "bar", hello: "world"}
%{foo: "bar", hello: "world"}

iex> %{foo: "bar", hello: "world"} == %{:foo => "bar", :hello => "world"}
true
```