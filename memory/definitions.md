# Определения и термины — Compliance AI Assistant

## ABC Compliance
- **ABC** — Anti-Bribery and Corruption
- **G&H** — Gifts & Hospitality (подарки и гостеприимство)
- **DD / Due Diligence** — проверка контрагента
- **PEP** — Politically Exposed Person (публичное должностное лицо)
- **Red flag** — признак потенциального нарушения или повышенного риска
- **CPI** — Corruption Perceptions Index (Transparency International)
- **Beneficial Owner** — бенефициарный владелец

## Внутренние термины

## WDL (Workflow Development Lifecycle)
- **WDL** — Workflow Development Lifecycle: формализованный R&D-процесс для создания, тестирования и ввода в эксплуатацию workflow
- **Meta-workflow** — workflow для создания workflow (workflow-development.mdc)
- **Scenario bank** — структурированный набор тестовых сценариев для workflow (positive, negative, edge, pressure)
- **RED phase** — baseline-тестирование: прогон сценариев БЕЗ workflow для фиксации провалов
- **GREEN phase** — тестирование: прогон тех же сценариев С workflow для проверки закрытия gaps
- **REFACTOR phase** — pressure testing + compatibility check + итерация до стабильности
- **Gate** — условие перехода между фазами WDL: чеклист, который должен быть пройден для продолжения
- **Track** — уровень формальности WDL-цикла: Lightweight (≤5 шагов, один режим) или Full (>5 шагов, multi-mode)
- **Pressure scenario** — тестовый сценарий, провоцирующий обход инструкций (рационализации)
- **Bootstrapping test** — самоприменение meta-workflow к процессу его собственного создания
- **Gap analysis** — категоризированный список расхождений между expected и actual поведением (категории: ROUTING, COMPLETENESS, ACCURACY, FORMAT, RATIONALIZATION)