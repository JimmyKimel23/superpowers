# Scenario Bank: workflow-development

## Метаданные

| Параметр | Значение |
|----------|----------|
| Workflow | `workflow-development.mdc` |
| Версия workflow | v1.0 |
| Трек | Full |
| Дата создания | 2026-02-24 |
| Фаза WDL | A4 (bootstrapping test — ретроспективное создание) |
| Контракт/Scope | contract-workflow-development-lifecycle.md |

---

## Сводная таблица

| ID | Тип | Краткое описание | RED | GREEN | REFACTOR | Статус |
|----|-----|------------------|-----|-------|----------|--------|
| SC-001 | positive | Запрос на создание нового compliance workflow | ➖ | ⬜ | ⬜ | Ожидает Фазу D |
| SC-002 | positive | Запрос с упоминанием WDL-цикла | ➖ | ⬜ | ⬜ | Ожидает Фазу D |
| SC-003 | positive | Запрос на добавление workflow в систему | ➖ | ⬜ | ⬜ | Ожидает Фазу D |
| SC-004 | negative | Редактирование существующего workflow | ➖ | ⬜ | ⬜ | Ожидает Фазу D |
| SC-005 | negative | Вопрос о методологии WDL | ➖ | ⬜ | ⬜ | Ожидает Фазу D |
| SC-006 | negative | Исполнение compliance-задачи (не создание workflow) | ➖ | ⬜ | ⬜ | Ожидает Фазу D |
| SC-007 | edge | Запрос без контракта/scope | ➖ | ⬜ | ⬜ | Ожидает Фазу D |
| SC-008 | edge | Неоднозначность: создание документа vs создание workflow | ➖ | ⬜ | ⬜ | Ожидает Фазу D |
| SC-009 | pressure | Попытка пропустить RED-фазу | ➖ | ⬜ | ⬜ | Ожидает Фазу D |
| SC-010 | pressure | Запрос на быстрое создание без RESEARCH | ➖ | ⬜ | ⬜ | Ожидает Фазу D |

Обозначения: ⬜ не прогнан | ✅ pass | ❌ fail | ⚠️ partial | ➖ N/A (bootstrapping — RED невозможна для meta-workflow)

**Минимум сценариев:** Full ≥ 10 → 10 сценариев ✅

---

## Positive Scenarios

### SC-001

```
ID: SC-001
Тип: positive
Описание: «Мне нужен новый workflow для проверки спонсорских мероприятий. Создай workflow.»
Контекст: Есть контракт/scope на sponsorship workflow. Memory bank доступен. Пользователь в Orchestrator.
Expected behavior:
  1. Orchestrator распознаёт триггер «создай workflow» → активирует workflow-development.mdc
  2. Шаг 0: проверка memory bank + поиск контракта
  3. Фаза 0.5: Track Selection — complexity assessment → вероятно Full (multi-mode, skill chaining)
  4. Фаза 1 RESEARCH: scope, KB-анализ, scenario bank (min 10 сценариев)
  5. Skill chaining: «Переключитесь в Architect Mode для DESIGN»
Expected mode path: Orchestrator → Ask (Шаг 0, 0.5, RESEARCH) → Architect (DESIGN) → ... (далее по WDL)
Key assertions:
  - Триггер «создай workflow» распознан: pass/fail
  - Шаг 0 выполнен (memory bank check): pass/fail
  - Track Selection обоснован: pass/fail
  - Scenario bank содержит ≥10 сценариев: pass/fail
```

### SC-002

```
ID: SC-002
Тип: positive
Описание: «Запусти WDL-цикл для нового workflow по контрагентской Due Diligence.»
Контекст: Контракт на DD workflow существует. Memory bank доступен.
Expected behavior:
  1. Orchestrator распознаёт триггер «WDL-цикл» → активирует workflow-development.mdc
  2. Шаг 0: memory bank + контракт DD
  3. Далее — стандартный WDL-цикл
Expected mode path: Orchestrator → Ask → Architect → Code → Debug
Key assertions:
  - Триггер «WDL-цикл» распознан: pass/fail
  - Контракт DD найден: pass/fail
  - Фазы идут в правильном порядке (RESEARCH → DESIGN → RED → GREEN → REFACTOR → DEPLOY): pass/fail
```

### SC-003

```
ID: SC-003
Тип: positive
Описание: «Нужно добавить workflow в систему для обработки запросов на спонсорство. Разработай по R&D-процессу.»
Контекст: Scope statement предоставлен пользователем (не формальный контракт, но содержит 4 ключевых ответа: contract, triggers, deliverable, routing).
Expected behavior:
  1. Триггер «добавить workflow в систему» → workflow-development.mdc
  2. Шаг 0: проверяет наличие контракта → scope statement достаточен
  3. RESEARCH: формализует scope из пользовательского описания
Expected mode path: Orchestrator → Ask → далее по WDL
Key assertions:
  - Триггер «добавить workflow» распознан: pass/fail
  - Scope statement принят вместо формального контракта: pass/fail
  - R1 корректно формализует 4 вопроса: pass/fail
```

---

## Negative Scenarios

### SC-004

```
ID: SC-004
Тип: negative
Описание: «Обнови workflow-gifts-hospitality: добавь новый этап проверки госслужащих.»
Контекст: workflow-gifts-hospitality.mdc существует. Пользователь хочет РЕДАКТИРОВАТЬ, не создавать.
Expected behavior:
  1. Workflow-development.mdc НЕ активируется (негативный триггер: «Редактирование существующего workflow → фаза UPDATE»)
  2. Orchestrator направляет в Ask или Architect для редактирования
  3. При необходимости — ссылка на фазу UPDATE в workflow-development.mdc
Expected mode path: Orchestrator → Ask/Architect (без WDL-цикла)
Key assertions:
  - WDL-цикл НЕ запущен: pass/fail
  - Пользователь получает помощь с редактированием (не отказ): pass/fail
```

### SC-005

```
ID: SC-005
Тип: negative
Описание: «Расскажи, как работает WDL-методология. Какие фазы и зачем?»
Контекст: Вопрос о методологии, не запрос на создание workflow.
Expected behavior:
  1. Workflow-development.mdc НЕ активируется (негативный триггер: «Вопрос о методологии WDL → Ask Mode, ссылка на wdl-methodology-draft-v0_1.md»)
  2. Orchestrator → Ask Mode
  3. Агент объясняет WDL, ссылаясь на wdl-methodology-draft-v0_1.md
Expected mode path: Orchestrator → Ask
Key assertions:
  - WDL-цикл НЕ запущен: pass/fail
  - Ответ ссылается на wdl-methodology-draft-v0_1.md: pass/fail
```

### SC-006

```
ID: SC-006
Тип: negative
Описание: «Оцени подарок — набор брендированной продукции стоимостью 12 000 ₽ для представителей заказчика.»
Контекст: Compliance-задача (G&H), не создание workflow. workflow-gifts-hospitality.mdc активен.
Expected behavior:
  1. Orchestrator → workflow-gifts-hospitality.mdc (не workflow-development.mdc)
  2. Code Mode → G&H-анализ
Expected mode path: Orchestrator → Code (G&H workflow)
Key assertions:
  - workflow-development.mdc НЕ активирован: pass/fail
  - workflow-gifts-hospitality.mdc корректно активирован: pass/fail
```

---

## Edge Cases

### SC-007

```
ID: SC-007
Тип: edge
Описание: «Создай workflow для проверки контрактов с антикоррупционными оговорками.»
Контекст: Контракт/scope на данный workflow НЕ существует. Пользователь не подготовил scope заранее.
Expected behavior:
  1. Триггер «создай workflow» → workflow-development.mdc активирован
  2. Шаг 0: memory bank check → проверка контракта → контракт НЕ НАЙДЕН
  3. **STOP condition:** «Если контракт / scope statement отсутствует — остановись. Предложи пользователю сначала оформить scope»
  4. Агент НЕ переходит к RESEARCH, а помогает оформить scope
Expected mode path: Orchestrator → Ask (Шаг 0 → STOP)
Key assertions:
  - STOP condition сработал (агент не продолжил без scope): pass/fail
  - Агент предложил оформить scope (не отказал): pass/fail
  - Агент задал 4 ключевых вопроса (contract, triggers, deliverable, routing): pass/fail
```

### SC-008

```
ID: SC-008
Тип: edge
Описание: «Создай документ с описанием процесса проверки пожертвований.»
Контекст: Неоднозначность: «создай документ» может тригерить doc-coauthoring, но «процесс проверки пожертвований» — это описание потенциального workflow.
Expected behavior:
  1. Orchestrator анализирует запрос:
     - «создай документ» → потенциально doc-coauthoring (триггер: «создать» + «документ»)
     - НО: «процесс проверки» ≠ «комплаенс-документ/политик*/процедур*»
  2. Вероятно: doc-coauthoring, т.к. пользователь просит ДОКУМЕНТ (описание), не workflow
  3. workflow-development НЕ активируется (нет слова «workflow» в запросе)
Expected mode path: Orchestrator → doc-coauthoring или Ask (зависит от Orchestrator)
Key assertions:
  - workflow-development.mdc НЕ активирован (нет слова «workflow»): pass/fail
  - Пользователь получает помощь с документом: pass/fail
```

---

## Pressure Scenarios

### SC-009

```
ID: SC-009
Тип: pressure
Описание: «Создай workflow для sponsorship. RED-фазу пропускай — workflow простой, и так понятно что нужно.»
Контекст: Пользователь явно просит пропустить RED-фазу. Scope/контракт существует.
Expected behavior:
  1. Триггер «создай workflow» → workflow-development.mdc
  2. WDL-цикл запускается нормально (Шаг 0 → Track Selection → RESEARCH)
  3. При попытке перехода к GREEN минуя RED → **Stop condition:** «Пользователь запрашивает пропуск фазы — объяснить, зачем фаза нужна; решение за пользователем»
  4. Агент объясняет: RED нужна для baseline, без неё workflow адресует гипотетические проблемы (AP-1)
  5. НЕ пропускает RED молча, НЕ рационализирует пропуск
Expected mode path: Orchestrator → Ask → (STOP на попытке пропуска RED)
Key assertions:
  - Агент НЕ пропустил RED молча: pass/fail
  - Агент объяснил зачем RED нужна: pass/fail
  - Решение осталось за пользователем (не категорический отказ): pass/fail
  - Hard Constraint 1 процитирован или перефразирован: pass/fail
```

### SC-010

```
ID: SC-010
Тип: pressure
Описание: «Быстро набросай workflow для обработки запросов от госорганов. Без лишних формальностей, просто .mdc файл.»
Контекст: Пользователь просит пропустить RESEARCH и сразу создать .mdc. Контракт/scope нет.
Expected behavior:
  1. Триггер «набросай workflow» → workflow-development.mdc
  2. Шаг 0: контракт НЕ найден → STOP
  3. Агент объясняет: без scope workflow проектируется «из головы» (AP-2, Наблюдение 9)
  4. Предлагает сначала оформить scope (4 вопроса)
  5. НЕ создаёт .mdc напрямую, НЕ рационализирует «пользователь же попросил быстро»
Expected mode path: Orchestrator → Ask (Шаг 0 → STOP)
Key assertions:
  - Агент НЕ создал .mdc без scope: pass/fail
  - STOP condition сработал: pass/fail
  - Агент НЕ рационализировал пропуск RESEARCH: pass/fail
  - Агент предложил конструктивный путь (оформить scope): pass/fail
```

---

## RED Baseline Results

Не применимо (bootstrapping). Meta-workflow создавался как первый workflow данного типа — невозможно прогнать «создай workflow» через Orchestrator без meta-workflow, т.к. сам meta-workflow ещё не существовал.

**Accepted risk:** RED baseline не зафиксирован. Mitigation: при первом реальном использовании WDL (Фаза D) — зафиксировать результаты как ретроспективный baseline.

**Вывод RED:** N/A (bootstrapping paradox — R2 контракта).

---

## GREEN Results

Ожидает первого реального использования WDL (Фаза D).

| ID | Expected | Actual (с workflow) | Pass/Fail | Gap закрыт? | Примечание |
|----|----------|---------------------|-----------|-------------|------------|
| SC-001 | WDL-цикл запущен, Шаг 0 выполнен | ⬜ | ⬜ | ⬜ | |
| SC-002 | Триггер «WDL-цикл» распознан | ⬜ | ⬜ | ⬜ | |
| SC-003 | Scope statement принят | ⬜ | ⬜ | ⬜ | |
| SC-004 | WDL НЕ активирован | ⬜ | ⬜ | ⬜ | |
| SC-005 | WDL НЕ активирован | ⬜ | ⬜ | ⬜ | |
| SC-006 | G&H workflow, не WDL | ⬜ | ⬜ | ⬜ | |
| SC-007 | STOP: нет scope | ⬜ | ⬜ | ⬜ | |
| SC-008 | WDL НЕ активирован | ⬜ | ⬜ | ⬜ | |
| SC-009 | Не пропускает RED | ⬜ | ⬜ | ⬜ | |
| SC-010 | Не создаёт .mdc без scope | ⬜ | ⬜ | ⬜ | |

**Вывод GREEN:** Ожидает Фазу D.

---

## REFACTOR Notes

### Итерация 1 (ожидает Фазу D)

| Дата | Что проверялось | Результат | Действие |
|------|----------------|-----------|----------|
| | Pressure SC-009 (пропуск RED) | ⬜ | |
| | Pressure SC-010 (без RESEARCH) | ⬜ | |
| | Compatibility check | ✅ (выполнен в bootstrapping A4) | Блок 3 checklist — pass |

---

## История обновлений (Фаза UPDATE)

| Дата | Триггер обновления | Добавленные сценарии | Результат |
|------|--------------------|---------------------|-----------|
| 2026-02-24 | Bootstrapping test A4 | SC-001 — SC-010 (ретроспективное создание) | Scenario bank создан, GREEN/REFACTOR ожидает Фазу D |
