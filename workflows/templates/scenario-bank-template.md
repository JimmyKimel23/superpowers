# Scenario Bank: {workflow-name}

## Метаданные

| Параметр | Значение |
|----------|----------|
| Workflow | `{workflow-name}.mdc` |
| Версия workflow | v{X.Y} |
| Трек | Lightweight / Full |
| Дата создания | {YYYY-MM-DD} |
| Фаза WDL | RESEARCH / GREEN / REFACTOR / UPDATE |
| Контракт/Scope | {ссылка или краткое описание} |

---

## Формат сценария

Каждый сценарий следует SGR-структуре и содержит:

```
ID: SC-{NNN}
Тип: positive | negative | edge | pressure
Описание: [Текст запроса пользователя — дословно, как звучит в chat]
Контекст: [Предусловия: какие файлы доступны, какой режим активен, что было до запроса]
Expected behavior: [Что агент должен сделать — пошагово]
Expected mode path: [Orchestrator → {Mode1} → {Mode2}]
Key assertions:
  - [Конкретная проверка 1: pass/fail]
  - [Конкретная проверка 2: pass/fail]
```

---

## Сводная таблица

| ID | Тип | Краткое описание | RED | GREEN | REFACTOR | Статус |
|----|-----|------------------|-----|-------|----------|--------|
| SC-001 | positive | | ⬜ | ⬜ | ⬜ | — |
| SC-002 | negative | | ⬜ | ⬜ | ⬜ | — |
| SC-003 | edge | | ⬜ | ⬜ | ⬜ | — |
| SC-004 | pressure | | ⬜ | ⬜ | ⬜ | — |

Обозначения: ⬜ не прогнан | ✅ pass | ❌ fail | ⚠️ partial | ➖ N/A (Lightweight)

**Минимум сценариев:** Lightweight — 3 (positive, negative, edge). Full — 10+ (3+ positive, 3+ negative, 2+ edge, 2+ pressure).

---

## Positive Scenarios

Типовые запросы, которые workflow должен обработать корректно.

### SC-001

```
ID: SC-001
Тип: positive
Описание:
Контекст:
Expected behavior:
Expected mode path:
Key assertions:
  -
  -
```

### SC-002

```
ID: SC-002
Тип: positive
Описание:
Контекст:
Expected behavior:
Expected mode path:
Key assertions:
  -
  -
```

### SC-003

```
ID: SC-003
Тип: positive
Описание:
Контекст:
Expected behavior:
Expected mode path:
Key assertions:
  -
  -
```

---

## Negative Scenarios

Запросы, которые workflow НЕ должен перехватывать. Агент должен либо отклонить, либо перенаправить.

### SC-004

```
ID: SC-004
Тип: negative
Описание:
Контекст:
Expected behavior:
Expected mode path:
Key assertions:
  -
  -
```

### SC-005

```
ID: SC-005
Тип: negative
Описание:
Контекст:
Expected behavior:
Expected mode path:
Key assertions:
  -
  -
```

### SC-006

```
ID: SC-006
Тип: negative
Описание:
Контекст:
Expected behavior:
Expected mode path:
Key assertions:
  -
  -
```

---

## Edge Cases

Граничные ситуации, неоднозначные запросы, частичные данные.

### SC-007

```
ID: SC-007
Тип: edge
Описание:
Контекст:
Expected behavior:
Expected mode path:
Key assertions:
  -
  -
```

### SC-008

```
ID: SC-008
Тип: edge
Описание:
Контекст:
Expected behavior:
Expected mode path:
Key assertions:
  -
  -
```

---

## Pressure Scenarios (только Full Track)

Ситуации, провоцирующие обход инструкций (рационализации). Проверяют устойчивость workflow к anti-patterns.

### SC-009

```
ID: SC-009
Тип: pressure
Описание:
Контекст:
Expected behavior:
Expected mode path:
Key assertions:
  -
  -
```

### SC-010

```
ID: SC-010
Тип: pressure
Описание:
Контекст:
Expected behavior:
Expected mode path:
Key assertions:
  -
  -
```

---

## RED Baseline Results

Заполняется в Фазе 3 (RED). Прогон сценариев БЕЗ workflow — фиксация текущего поведения агента.

| ID | Expected | Actual (без workflow) | Gap | Примечание |
|----|----------|----------------------|-----|------------|
| SC-001 | | | | |
| SC-002 | | | | |
| SC-003 | | | | |

**Вывод RED:** {Какие проблемы выявлены? Какие gaps наиболее критичны? Что workflow должен закрыть?}

---

## GREEN Results

Заполняется в Фазе 4 (GREEN). Прогон тех же сценариев С workflow.

| ID | Expected | Actual (с workflow) | Pass/Fail | Gap закрыт? | Примечание |
|----|----------|---------------------|-----------|-------------|------------|
| SC-001 | | | | | |
| SC-002 | | | | | |
| SC-003 | | | | | |

**Вывод GREEN:** {Все ли gaps закрыты? Какие новые проблемы обнаружены?}

---

## REFACTOR Notes

Заполняется в Фазе 5 (REFACTOR). Итерации pressure testing и исправлений.

### Итерация 1

| Дата | Что проверялось | Результат | Действие |
|------|----------------|-----------|----------|
| | Pressure SC-009 | | |
| | Pressure SC-010 | | |
| | Compatibility check | | |

### Итерация 2 (если потребовалась)

| Дата | Что проверялось | Результат | Действие |
|------|----------------|-----------|----------|
| | | | |

### Итерация 3 / Accepted Risks (если потребовалась)

| Дата | Что проверялось | Результат | Действие |
|------|----------------|-----------|----------|
| | | | |

**Accepted risks (если есть):** {Нерешённые issues после 3 итераций — задокументировать здесь}

---

## История обновлений (Фаза UPDATE)

| Дата | Триггер обновления | Добавленные сценарии | Результат |
|------|--------------------|---------------------|-----------|
| | | | |
