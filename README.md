# Power BI

# 1. Импорт и первичная очистка (Power Query)

## Задачи

- Загрузить CSV-файл с вакансиями через **Power Query**.
- Привести типы данных к корректным форматам:
  - дата;
  - число;
  - текст.
- Очистить значения:
  - заменить `"Не указана"`, пустые строки и `0` на `null`.
- Удалить дубликаты вакансий по `id`, оставив запись с самой поздней датой `published`.
- Добавить вычисляемый столбец:

```text
salary_avg = (salary_from + salary_to) / 2
```

- Для столбца `key_skills`:
  - разделить строку по запятой;
  - удалить дубликаты навыков внутри одной вакансии;
  - создать столбец `skills_count` — количество навыков.

### Используемые инструменты Power Query

- Replace Values
- Remove Duplicates
- Split Column
- Group By
- Add Column
- Custom Column
- Text Transform

---

# 2. Обогащение данных и построение модели

## Задачи

Создать вычисляемую колонку **salary_band**:

- До 100 000
- 100 000–200 000
- 200 000+

Создать таблицы-справочники:

- `dim_city`
- `dim_technology`
- `dim_employment`

Связать их с таблицей `vacancies` отношениями **One-to-Many**.

Создать вычисляемую таблицу `skills_exploded`, где каждая строка представляет отдельный навык.

Можно использовать:

- Power Query;
- DAX (`SELECTCOLUMNS`, `UNION`).

Добавить конвертацию валют:

- создать справочник курсов валют;
- использовать `RELATED()` для пересчета `salary_avg` в рубли.

---

# 3. Расширенные вычисления (DAX)

## Примеры мер

```DAX
Avg_Salary =
AVERAGE(vacancies[salary_avg])

Count_Vacancies =
COUNT(vacancies[id])

Vacancies_Per_Tech =
COUNTROWS(RELATEDTABLE(vacancies))

Top_Skill =
CALCULATE(
    COUNTROWS(skills_exploded),
    TOPN(1, skills_exploded, [Count], DESC)
)

Published_Last_30_Days =
CALCULATE(
    [Count_Vacancies],
    FILTER(
        vacancies,
        vacancies[published] >= TODAY() - 30
    )
)
```

## Требование

Создать **не менее 5 мер**, используя функции:

- `CALCULATE`
- `FILTER`
- `DISTINCTCOUNT`
- `RANKX`
- `VAR`

---

# 4. Визуализация

Необходимо создать **2 страницы** дашборда.

## Страница 1 — Общая аналитика рынка

### Визуализации

- Карта вакансий по городам (количество вакансий).
- Диаграмма **Top-10 технологий**.
- Средние зарплаты по диапазонам `salary_band`.

### Слайсеры

- Город
- Технология
- Опыт (`experience`)
- Тип занятости (`employment`)

---

## Страница 2 — Навыки и зарплаты

### Визуализации

- Word Cloud по ключевым навыкам (Custom Visual).
- Диаграмма **Опыт vs Зарплата** (Scatter Chart или Box Plot).
- Линейный график динамики вакансий по дате `published`.

### KPI

- Средняя зарплата.
- Количество вакансий за последние 30 дней.
- Средняя длительность вакансии (если есть даты обновления).