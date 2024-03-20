# Описание API методов

Общение микросервисов фронтенда и бэкенда основано на протоколе Graphql, а там используются только Get и Post методы HTTP для запросов, поэтому в этой лабораторной работе не будет Put/Patch/Delete методов.

## Public API HTTP REST

Для интеграции сторонних сервисов через API реализованы методы увеличения показателей сотрудников и участие в событиях, они описаны ниже.

Для валидации всех запросов используется токен API, который задается в ENV переменных и указывается при развертывании в конкретной компании.

```yaml
Headers:
    Authorization: Bearer <token>
```

### Увеличение показателя

Используется для увеличения показателей сотрудника (например - +1 к количеству выполненных сотрудников задач)

```yaml
[POST] /users/stats

{
    stat_id: Int64, # Берется из интерфейса фронта
    user_email: String # Имейл сотрудника
}
```

### Участие сотрудника в событии

Используется для присваивания сотруднику участие в событии (например - поучаствовал в новогоднем корпоративе 2023 года)

```yaml
[POST] /users/events

{
    event_id: Int64, # Берется из интерфейса фронта
    user_email: String # Имейл сотрудника
}
```

## Private GraphQL API

Для интеграции фронтенд и бэкенд сервисов используется Graphql, поскольку их огромное количество (посмотреть все можно [тут](https://github.com/Gamification-team-HSE/gamification-frontend/tree/main/src/graphql)). Тут будут расписаны только манипуляции (CRUD на GraphQL) с показателями.

Для авторизации запросов также используется заголовок, токен к нему получает клиентский сервис на основе авторизации через Email код.

```yaml
Headers:
    Authorization: Bearer <token>
```

### Получение одного показателя

**Payload:**

```yaml
{
    id: Int!
}
```

**Result:**

```yaml
{
    id: Int!,
    name: String!,
    description: String,
    created_at: Int!,
    start_at: Int!,
    period: String!,
    seq_period: String
}
```

### Создание показателя

**Payload:**

```yaml
{
    name: String!,
    description: String,
    start_at: Int!,
    period: String!,
    seq_period: String
}
```

**Result:** пустой если все ок или ошибка

### Обновление показателя

**Payload:**

```yaml
{
    id: Int!,
    name: String,
    description: String,
    start_at: Int,
    period: String,
    seq_period: String
}
```

**Result:** пустой если все ок или ошибка

### Удаление показателя

**Payload:**

```yaml
{
    id: Int!
}
```

**Result:** пустой если все ок или ошибка

### Получение нескольких показателей

**Payload:**

```yaml
{
    page: Int!,
    size: Int!
}
```

**Result:**

```yaml
{
    total: Int!,
    stats: [Stat!]!

    # Раскрытие объекта Stat (единтичен получению одного объекта выше)
    Stat: {
        id: Int!,
        name: String!,
        description: String,
        created_at: Int!,
        start_at: Int!,
        period: String!,
        seq_period: String
    }
}
```

### Получение рейтинга сотрудников по показателю

```yaml
{
    id: Int!, # Stat ID
}
```

**Result:**

```yaml
{
    stat_id: Int!,
    total: Int!,
    users: [UserRatingByStat!]!

    # Раскрытие объекта UserRatingByStat
    UserRatingByStat: {
        user_id: Int!,
        name: String,
        email: String!,
        avatar: String,
        place: Int!,
        value: Int!
    }
}
```
