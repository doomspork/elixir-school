---
layout: page
title: OTP Супервизоры
category: advanced
order: 6
lang: ru
---

Супервизоры это специальные процессы с единственной целью: мониторинг других процессов. Супервизоры позволяют нам создавать отказоустойчивые приложения при помощи автоматического перезапуска дочерних процессов в случае их отказа.

{% include toc.html %}

## Настройка

Магия супервизоров заключена в функции `Supervisor.start_link/2`.  Помимо запуска нашего супервизора и его потомков, она позволяет нам определять стратегию, которую будет использовать наш супервизор для управления дочерними процессами.

Потомки определяются при помощи списка и функции `worker/3`, которую мы импортировали из `Supervisor.Spec`.  Функция `worker/3` принимает модуль, аргументы и набор опций.  Под капотом `worker/3` вызывает `start_link/3` с нашими аргументами во время инициализации.

Давайте начнём, используя SimpleQueue из урока [OTP Concurrency](/lessons/advanced/otp-concurrency):

```elixir
import Supervisor.Spec

children = [
  worker(SimpleQueue, [], [name: SimpleQueue])
]

{:ok, pid} = Supervisor.start_link(children, strategy: :one_for_one)
```

Если наш процесс упадёт или завершится, супервизор автоматически перезапустит его как ни в чём не бывало.

### Стратегии

На данный момент для Супервизора доступны четыре разные стратегии перезапуска:

+ `:one_for_one` - Перезапуск только упавшего дочернего процесса.

+ `:one_for_all` - Перезапуск всех дочерних процессов из события падения.

+ `:rest_for_one` - Перезапуск упавшего процесса и всех процессов, запущенных после него.

+ `:simple_one_for_one` - Наилучшая для динамически прикрепляемых процессов. Супервизор обязан содержать только одного потомка.

### Вложенность

Помимо воркеров, мы также можем наблюдать за другими супервизорами, создавая дерево надзора.  Единственное отличие для нас &mdash; замена `supervisor/3` на `worker/3`:

```elixir
import Supervisor.Spec

children = [
  supervisor(ExampleApp.ConnectionSupervisor, [[name: ExampleApp.ConnectionSupervisor]]),
  worker(SimpleQueue, [[], [name: SimpleQueue]])
]

{:ok, pid} = Supervisor.start_link(children, strategy: :one_for_one)
```

## Наблюдатель задач

Для задач есть специальный супервизор &mdash; `Task.Supervisor`.  Он разработан для динамически создаваемых задач и под капотом использует `:simple_one_for_one`.

### Настройка

Включение `Task.Supervisor` ничем не отличается от других супервизоров:

```elixir
import Supervisor.Spec

children = [
  supervisor(Task.Supervisor, [[name: ExampleApp.TaskSupervisor]]),
]

{:ok, pid} = Supervisor.start_link(children, strategy: :one_for_one)
```

### Наблюдаемые задачи

При запущенном супервизоре мы можем использовать функцию `start_child/2` для создания наблюдаемой задачи:

```elixir
{:ok, pid} = Task.Supervisor.start_child(ExampleApp.TaskSupervisor, fn -> background_work end)
```

Если наша задача преждевременно выйдет из строя, она будет перезапущена.  Это может быть особенно полезно при работе со входящими соединениями и обработке фоновых задач.
