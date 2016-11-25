---
layout: page
title: ������������
category: advanced
order: 10
lang: gr
---

��� ����������� ������ ������ ��� ��� ������������, �� ���� �� ������� ��� �� ��������� ��� ��� ������� �� �������� ����� ��� ������������.  ���� Elixir, ���� � ���������� ���������� �� ������������.

{% include toc.html %}

## �����������

���� ������ �� �������� ��� �� ����������� ��� ������� API, � ���� ��� ���� ���� Elixir ����� �� ������������.  �� ������������ ����� ��� ������ ������:

+ ������� ���� ��� ����������� ��� ������ �� �����������
+ ������� ��� ��� ��������� ����� ��� ���

� Elixir �������� ��� ������ ������������ ���� ���� ��� GenServer, ���� �� ���� �� ������ �� ���������� ��� ���������� ����.

## ��������� ��� �����������

��� �� ������������ �������� ��� ������������, �� ������������ ��� ��� ��� ������� ������.  ����� �� ������� �� ������ �� ��������� ��� �����������: ��� `init/1` ��� `perform/2`.

��� �� ����������� ����, �� ��������������� ��� ������ `@callback` �� ���������� �������� ��� `@spec`.  ���� ������ ��� __��������__ ������ - ��� ��� ������������ �������� �� ���������������� ��� `@macrocallback`.  �� ����������� ��� �������� `init/1` ��� `perform/2` ��� ���� ������� ���:

```elixir
defmodule Example.Worker do
  @callback init(state :: term) :: {:ok, new_state :: term} | {:error, reason :: term}
  @callback perform(args :: term, state :: term) :: {:ok, result :: term, new_state :: term} | {:error, reason :: term, new_state :: term}
end
```

��� ������� ��� `init/1` ��� �� ���������� ���� ���� ��� �� ���������� ��� ������ ��� ��� `{:ok, state}` � `{:error, reason}`, ��� ���� ����������� ������������.  � ������� `perform/2` �� �������� ������ �������� ��� ��� ������ ���� �� ��� ��������� ��� ��������������, ��� �� ����������� ��� ��� `perform/2` �� ���������� `{:ok, result, state}` � `{:error, reason, state}` ���� ������� ��� �� GenServers.

## ����� ������������

���� ��� ������ ������ ��� ����������� ��� �������� �� ��� ���������������� �� ��� ����� �������� ��� ���� ����������� �� ���� ������� API.  � �������� ���� ������������ ���� ������� ��� ����� ������ �� �� �������������� `@behaviour`.

��������������� �� ��� ��� ����������� �� �������������� ��� ������� ��� �� ��������� ��� ������������� ������ ��� �� �� ���������� ������:

```elixir
defmodule Example.Downloader do
  @behaviour Example.Worker

  def init(opts), do: {:ok, opts}

  def perform(url, opts) do
    url
    |> HTTPoison.get!
    |> Map.fetch(:body)
    |> write_file(opts[:path])
    |> respond(opts)
  end

  defp write_file(:error, _), do: {:error, :missing_body}
  defp write_file({:ok, contents}, path) do
    path
    |> Path.expand
    |> File.write(contents)
  end

  defp respond(:ok, opts), do: {:ok, opts[:path], opts}
  defp respond({:error, reason}, opts), do: {:error, reason, opts}
end
```

� �� �� ������ ��� ���� ������ ��� ��������� ��� ������ �������;  ����� ������:

```elixir
defmodule Example.Compressor do
  @behaviour Example.Worker

  def init(opts), do: {:ok, opts}

  def perform(payload, opts) do
    payload
    |> compress
    |> respond(opts)
  end

  defp compress({name, files}), do: :zip.create(name, files)

  defp respond({:ok, path}, opts), do: {:ok, path, opts}
  defp respond({:error, reason}, opts), do: {:error, reason, opts}
end
```

������ ��� � ������� ��� ������� ����� �����������, �� ������� API ��� �������� ��� �����, ��� ���� ������� ��� ��������� ����� ��� �������� ������ �� ����������� ���� ���� ����������� ��� �� ��������������� ���� ������. ���� ��� ����� �� ���������� �� �������������� ����� ������� �������, �� ��� ������ �� ����� ������������ ��������, ���� ���� �� �������������� ��� ���� ������� API.

�� ����� ��� ����������� ��� ����������� ���� ���������� ��� �� ������������ ��� ��������� �����������, �� ������� ��� ������������� ���� ��� �������. ��� �� �� ����� ���� ���� ����� �� ��������� ��� ������ ��� ���� ������� `Example.Compressor` ���������� �� ��������� `init/1`:

```elixir
defmodule Example.Compressor do
  @behaviour Example.Worker

  def perform(payload, opts) do
    payload
    |> compress
    |> respond(opts)
  end

  defp compress({name, files}), do: :zip.create(name, files)

  defp respond({:ok, path}, opts), do: {:ok, path, opts}
  defp respond({:error, reason}, opts), do: {:error, reason, opts}
end
```

���� �� ������ �� ����� ��� ������������� ���� �� ������� ��� ������ ���:

```shell
lib/example/compressor.ex:1: warning: undefined behaviour function init/1 (for behaviour Example.Worker)
Compiled lib/example/compressor.ex
```

���� ����! ���� ������� ������� �� ���������� ��� ����������� ������������ �� ������.
