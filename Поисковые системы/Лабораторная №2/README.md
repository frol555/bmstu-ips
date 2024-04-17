# bmstu-ips

## Именованные сущности

1. PERSON

    Сущность ниже описывает структуру фактов, связанных с людьми:

    Person: Это факт, который имеет два атрибута: position (должность) и name (имя).

    Name: Этот факт представляет собой имя человека и состоит из двух частей: first (имя) и last (фамилия).

    POSITION: Здесь мы определяем список должностей, таких как "энтомолог", "президент" и "жена".

    NAME: Это правило, которое связывает грамматические анализаторы для имени и фамилии. Оно интерпретирует их как атрибуты факта Name.

    PERSON: Это правило объединяет должность и имя, чтобы создать факт Person.

    ```python
    Person = fact(
        'Person',
        ['position', 'name']
    )

    Name = fact(
        'Name',
        ['first', 'last']
    )


    POSITION = morph_pipeline([
        'энтомолог',
        'президент',
        'жена'
    ])

    NAME = rule(
        gram('Name').interpretation(
            Name.first.inflected()
        ),
        gram('Surn').interpretation(
            Name.last.inflected()
        )
    ).interpretation(
        Name
    )

    PERSON = rule(
        POSITION.interpretation(
            Person.position.inflected()
        ),
        NAME.interpretation(
            Person.name
        )
    ).interpretation(
        Person
    )
    ```

2. DATE

    Сущность ниже описывает структуру фактов, связанных с датами:

    Date: Это факт, который содержит три атрибута: year (год), month (месяц) и day (день).

    MONTHS: Здесь определены названия месяцев на русском языке.

    MONTH_NAME: Это словарь, который связывает названия месяцев с фактом Date.month.

    DAY: Это правило, которое интерпретирует числа от 1 до 31 как атрибут Date.day.

    YEAR: Это правило, которое интерпретирует числа от 1800 до 2100 как атрибут Date.year.

    DATE: Это правило объединяет день, месяц и год, чтобы создать факт Date.

    ```python
    Date = fact(
        'Date',
        ['year', 'month', 'day']
    )


    MONTHS = {
        'январь',
        'февраль',
        'март',
        'апрель',
        'мая',
        'июнь',
        'июль',
        'август',
        'сентябрь',
        'октябрь',
        'ноябрь',
        'декабрь'
    }


    MONTH_NAME = dictionary(MONTHS)
    DAY = and_(
        gte(1),
        lte(31)
    )
    YEAR = and_(
        gte(1800),
        lte(2100)
    )
    DATE = rule(
        DAY.interpretation(
            Date.day
        ),
        MONTH_NAME.interpretation(
            Date.month
        ),
        YEAR.interpretation(
            Date.year
        ).optional()
    ).interpretation(
        Date
    )
    ```

3. ACTION

    Сущность ниже описывает структуру фактов, связанных с действиями или событиями в определенный год:

    Action: Это факт, который имеет два атрибута: titles (заголовки) и year (год).

    Y: Здесь определен список слов, связанных с годом, например, "год".

    TITLE1: Это правило, которое интерпретирует названия, связанные с Альбертом Эйнштейном, такие как "Альберт Эйнштейн", "Эйнштейн" и "он".

    YEAR: Это правило, которое интерпретирует числа от 1800 до 2100 как атрибут Action.year.

    DATE: Это правило, которое интерпретирует год как атрибут Action.year.

    ACTION: Это правило объединяет год и заголовок, чтобы создать факт Action.

    ```python
    Action = fact(
        'Action',
        ['titles', 'year']
    )

    Y = morph_pipeline(['год'])

    TITLE1 = rule(
        Y,
        dictionary({
            'Альберт Эйнштейн',
            'Эйнштейн',
            'он'
        }),
        not_(eq('.')).repeatable(),
    )


    YEAR = and_(
        gte(1800),
        lte(2100)
    )
    DATE = rule(
        YEAR.interpretation(
            Action.year
        )
    )

    ACTION = rule(
        DATE.interpretation(
            Action.year
        ),
        TITLE1.interpretation(
            Action.titles
        )
    ).interpretation(
        Action
    )
    ```
