import { useState } from "react";

const COLORS = {
  platform: { bg: "#1a1a2e", border: "#e94560", text: "#e94560", light: "#e9456020" },
  layer1: { bg: "#16213e", border: "#0f3460", text: "#a8d8ea", light: "#0f346030" },
  layer2: { bg: "#1a1a2e", border: "#533483", text: "#c4b7e6", light: "#53348320" },
  layer3: { bg: "#1a1a2e", border: "#0f3460", text: "#a8d8ea", light: "#0f346020" },
  workflow: { bg: "#1a1a2e", border: "#00b4d8", text: "#90e0ef", light: "#00b4d820" },
  memory: { bg: "#1a1a2e", border: "#e9c46a", text: "#e9c46a", light: "#e9c46a15" },
  kb: { bg: "#1a1a2e", border: "#2a9d8f", text: "#8ecdc8", light: "#2a9d8f20" },
  wdl: { bg: "#1a1a2e", border: "#f4a261", text: "#f4a261", light: "#f4a26120" },
};

const STABILITY = {
  immutable: { label: "IMMUTABLE", color: "#e94560", desc: "Определяется платформой. Не меняется." },
  stable: { label: "STABLE", color: "#0f3460", desc: "Меняется редко. Изменения требуют аудита." },
  evolving: { label: "EVOLVING", color: "#f4a261", desc: "Меняется по мере R&D. Управляемые изменения." },
  dynamic: { label: "DYNAMIC", color: "#00b4d8", desc: "Растёт постоянно. Основная зона работы." },
};

const COMPONENTS = {
  platform: {
    id: "platform",
    name: "Платформа (Yandex Code Assistant)",
    stability: "immutable",
    type: "platform",
    items: [
      "5 секций системного промпта",
      "Один tool call за сообщение",
      "Ожидание подтверждения между tool calls",
      "alwaysApply = видимость, не enforcement",
      "Architect: только .md файлы (regex)",
      "Нет subagents, ручное переключение",
      "Встроенный update_todo_list",
    ],
    dependsOn: [],
    influences: ["layer1", "modes", "workflows", "rules"],
    breakRadius: "ВСЁ",
    contract: "Неизменяемо. Все остальные компоненты адаптируются к платформе.",
  },
  layer1: {
    id: "layer1",
    name: "Слой 1: Base Layer",
    subtitle: "Custom Instructions for All Modes",
    stability: "stable",
    type: "layer1",
    items: [
      "10 принципов (точность, источники, SGR, приоритеты...)",
      "Language policy (RU/EN)",
      "Uncertainty protocol",
      "Output style",
      "Memory protocol",
    ],
    dependsOn: ["platform"],
    influences: ["modes", "workflows"],
    breakRadius: "Все режимы + все workflows",
    contract: "Принципы стабильны. Новый принцип = аудит совместимости со всеми modes.",
  },
  modes: {
    id: "modes",
    name: "Слой 2: Mode Instructions",
    subtitle: "5 режимов с контрактами",
    stability: "stable",
    type: "layer2",
    items: [
      "Architect: двухшаговый процесс, Design Review, WDL-awareness",
      "Code: пятифазный цикл, SGR-шаблон findings",
      "Ask: градация ответов, red flags, без модификации файлов",
      "Debug: 3 контекста (Compliance QA / Code / Workflow QA)",
      "Orchestrator: маршрутизация, прогресс-трекинг, workflow enforcement",
    ],
    dependsOn: ["platform", "layer1"],
    influences: ["workflows"],
    breakRadius: "Workflows, использующие конкретный режим",
    contract: "Каждый mode имеет контракт (Role + When to Use + Instructions). Workflow зависит от контракта mode, не от внутренней реализации.",
  },
  rules: {
    id: "rules",
    name: "Слой 3: Rules (.mdc)",
    subtitle: "3 правила",
    stability: "stable",
    type: "layer3",
    items: [
      "rule-memory-bank-usage (alwaysApply) — справочный контекст",
      "rule-single-stage-execution — по запросу",
      "rule-all-stages-execution — по запросу",
    ],
    dependsOn: ["platform"],
    influences: [],
    breakRadius: "Минимальный — rules = visibility, не enforcement",
    contract: "Rules не контролируют поток. Для enforcement — встраивать в workflow (Шаг 0).",
  },
  workflows: {
    id: "workflows",
    name: "Слой 3: Workflows (.mdc)",
    subtitle: "8 production workflows",
    stability: "dynamic",
    type: "workflow",
    items: [
      "gifts-hospitality (v2.1, ✅ routing, ✅ tested)",
      "donations (v2, ❌ routing)",
      "sponsorship (v1, ✅ routing, ✅ WDL-certified)",
      "ako-validation (❌ routing)",
      "compliance-letter-contragent",
      "ticket-recon",
      "doc-coauthoring",
      "workflow-development (meta-workflow)",
    ],
    dependsOn: ["platform", "layer1", "modes", "memory", "kb"],
    influences: ["memory"],
    breakRadius: "Только конкретный workflow. Изоляция = ключевое свойство.",
    contract: "Обязательный интерфейс: frontmatter + Step 0 + routing table + SGR steps + stop conditions + anti-patterns + verification + output format.",
  },
  memory: {
    id: "memory",
    name: "Memory Layer",
    subtitle: ".codeassistant/memory/",
    stability: "dynamic",
    type: "memory",
    items: [
      "index.md — навигационная карта (точка входа)",
      "lessons-learned.md — ошибки, выводы, решения",
      "patterns.md — рабочие паттерны + рационализации",
      "definitions.md — термины и определения",
    ],
    dependsOn: [],
    influences: [],
    breakRadius: "Нулевой — memory = информационный слой, ничего не ломает",
    contract: "Чтение через Шаг 0 в workflows. Обновление — по результатам задач. Context debt = главный риск.",
  },
  kb: {
    id: "kb",
    name: "Knowledge Base",
    subtitle: "knowledge-base/",
    stability: "evolving",
    type: "kb",
    items: [
      "policies/ — внутренние политики (источник правды)",
      "processes/ — описания процессов (G&H, AKO...)",
      "Шаблоны, примеры, дополнительные источники",
    ],
    dependsOn: [],
    influences: ["workflows"],
    breakRadius: "Workflows, ссылающиеся на конкретные KB-файлы",
    contract: "Workflows ссылаются на KB по пути. Переименование файла = сломанная зависимость. Решение: верификация через list_files (Шаг 0.5).",
  },
  wdl: {
    id: "wdl",
    name: "WDL Methodology",
    subtitle: "Meta-уровень: R&D процесс",
    stability: "evolving",
    type: "wdl",
    items: [
      "WDL-методология (7 фаз: RESEARCH → DEPLOY → UPDATE)",
      "Контракт WDL (scope + acceptance criteria)",
      "Deployment checklist (6 блоков верификации)",
      "Scenario bank template",
      "workflow-development.mdc (meta-workflow)",
    ],
    dependsOn: ["modes", "rules"],
    influences: ["workflows"],
    breakRadius: "Процесс создания workflows. Не влияет на существующие.",
    contract: "WDL определяет КАК создавать workflows. Не определяет ЧТО внутри конкретного workflow.",
  },
};

const DEPENDENCY_LINES = [
  { from: "platform", to: "layer1", label: "ограничивает", critical: true },
  { from: "platform", to: "modes", label: "ограничивает", critical: true },
  { from: "layer1", to: "modes", label: "принципы", critical: true },
  { from: "layer1", to: "workflows", label: "принципы", critical: false },
  { from: "modes", to: "workflows", label: "контракты режимов", critical: true },
  { from: "kb", to: "workflows", label: "KB-файлы", critical: false },
  { from: "memory", to: "workflows", label: "Шаг 0 (чтение)", critical: false },
  { from: "wdl", to: "workflows", label: "процесс создания", critical: false },
  { from: "workflows", to: "memory", label: "обновление", critical: false },
];

const INTERFACE_CONTRACT = [
  { section: "Frontmatter", fields: ["description", "triggers", "alwaysApply/globs"], required: true },
  { section: "Назначение + Версия", fields: ["Что делает (1-3 предложения)", "Номер версии"], required: true },
  { section: "Триггеры / Не-триггеры", fields: ["Позитивные триггеры", "Негативные триггеры"], required: true },
  { section: "Таблица маршрутизации", fields: ["Этап → Режим → Что происходит", "Skill chaining инструкции"], required: true },
  { section: "Шаг 0: Подготовка", fields: ["Memory bank check", "KB-верификация (list_files)", "Входные документы"], required: true },
  { section: "Шаги (SGR)", fields: ["Текущее состояние", "Инструкции", "Итог шага"], required: true },
  { section: "Stop Conditions", fields: ["Когда остановиться и спросить"], required: true },
  { section: "Антипаттерны", fields: ["Минимум 3 «НЕ делай»"], required: true },
  { section: "Верификация", fields: ["Self-check чеклист", "Ссылка на Debug"], required: true },
  { section: "Формат результата", fields: ["Структура выходного файла", "Путь сохранения"], required: true },
];

const INFLUENCE_SCENARIOS = [
  {
    trigger: "Изменение принципа в Слое 1",
    impact: "high",
    affected: ["Все 5 modes", "Все 8 workflows"],
    action: "Полный аудит: проверить совместимость каждого mode и workflow с новой формулировкой",
    example: "Добавление принципа 10 (ПОМОЩЬ, НЕ ЗАМЕНА) потребовало маркировки во всех compliance-output",
  },
  {
    trigger: "Изменение контракта режима",
    impact: "medium",
    affected: ["Workflows, использующие этот режим"],
    action: "Проверить: workflow ссылается на контракт mode или на реализацию?",
    example: "Добавление пятифазного цикла в Code не сломало workflows — они зависят от «Code создаёт файл», не от «5 фаз»",
  },
  {
    trigger: "Новый workflow",
    impact: "low",
    affected: ["index.md (навигация)", "Orchestrator (triggers)"],
    action: "Проверить: triggers не пересекаются, index.md обновлён",
    example: "workflow-sponsorship добавлен без влияния на остальные workflows",
  },
  {
    trigger: "Переименование KB-файла",
    impact: "medium",
    affected: ["Workflows со ссылками на этот файл"],
    action: "Найти и обновить все ссылки. Шаг 0.5 (list_files) ловит это в runtime.",
    example: "Если G&H.txt переименован → workflow-gifts-hospitality не найдёт файл на Шаге 0",
  },
  {
    trigger: "Рост memory bank",
    impact: "low",
    affected: ["Контекстное окно агента"],
    action: "Compression cycle: hot/cold разделение, сжатие закрытых наблюдений",
    example: "11 наблюдений + 14 паттернов — пока управляемо, мониторить",
  },
];

const ISOLATION_PRINCIPLES = [
  { 
    principle: "Workflow изолирован от других workflows", 
    mechanism: "Нет cross-references между workflows. Общий контракт — через Шаг 0 и modes.",
    status: "working"
  },
  { 
    principle: "Mode изолирован от конкретных workflows", 
    mechanism: "Mode определяет КАК работать (цикл, формат). Workflow определяет ЧТО делать.",
    status: "working"
  },
  { 
    principle: "KB изолирован от архитектуры", 
    mechanism: "KB = данные. Архитектура = процесс. Связь через пути файлов.",
    status: "fragile",
    risk: "Жёсткие пути к файлам. Переименование = сломанная ссылка."
  },
  { 
    principle: "Memory изолирован от execution", 
    mechanism: "Memory = информационный слой. Чтение опционально (через Шаг 0). Не влияет на логику.",
    status: "working"
  },
  { 
    principle: "WDL изолирован от production workflows", 
    mechanism: "WDL = процесс создания. Не влияет на уже deployed workflows.",
    status: "working"
  },
  { 
    principle: "Слой 1 НЕ изолирован от modes и workflows", 
    mechanism: "Принципы пронизывают всё. Это by design, но = главная зона каскадных изменений.",
    status: "by-design",
    risk: "Каждый новый принцип = потенциальный каскад. Порог: 10 принципов — уже на границе."
  },
];

function Badge({ children, color }) {
  return (
    <span style={{
      display: "inline-block",
      padding: "2px 8px",
      borderRadius: "3px",
      fontSize: "10px",
      fontWeight: 700,
      letterSpacing: "0.5px",
      textTransform: "uppercase",
      background: color + "20",
      color: color,
      border: `1px solid ${color}40`,
    }}>
      {children}
    </span>
  );
}

function StabilityBadge({ level }) {
  const s = STABILITY[level];
  return <Badge color={s.color}>{s.label}</Badge>;
}

function ImpactBadge({ level }) {
  const colors = { high: "#e94560", medium: "#f4a261", low: "#2a9d8f" };
  const labels = { high: "HIGH", medium: "MEDIUM", low: "LOW" };
  return <Badge color={colors[level]}>{labels[level]}</Badge>;
}

function StatusDot({ status }) {
  const map = {
    working: { color: "#2a9d8f", label: "Работает" },
    fragile: { color: "#f4a261", label: "Хрупкий" },
    "by-design": { color: "#533483", label: "By design" },
  };
  const s = map[status];
  return (
    <span style={{ display: "inline-flex", alignItems: "center", gap: 4, fontSize: 11, color: s.color }}>
      <span style={{ width: 6, height: 6, borderRadius: "50%", background: s.color, display: "inline-block" }} />
      {s.label}
    </span>
  );
}

function ComponentCard({ comp, isSelected, onClick }) {
  const c = COLORS[comp.type];
  return (
    <div
      onClick={onClick}
      style={{
        background: isSelected ? c.light : c.bg,
        border: `1px solid ${isSelected ? c.border : c.border + "60"}`,
        borderRadius: 6,
        padding: "12px 14px",
        cursor: "pointer",
        transition: "all 0.2s",
        opacity: isSelected ? 1 : 0.85,
      }}
    >
      <div style={{ display: "flex", justifyContent: "space-between", alignItems: "flex-start", marginBottom: 4 }}>
        <span style={{ color: c.text, fontSize: 13, fontWeight: 700, lineHeight: 1.3 }}>{comp.name}</span>
        <StabilityBadge level={comp.stability} />
      </div>
      {comp.subtitle && (
        <div style={{ color: "#888", fontSize: 11, marginBottom: 6, fontStyle: "italic" }}>{comp.subtitle}</div>
      )}
      <div style={{ display: "flex", flexWrap: "wrap", gap: 3 }}>
        {comp.items.slice(0, 3).map((item, i) => (
          <span key={i} style={{ fontSize: 10, color: "#999", background: "#ffffff08", padding: "1px 5px", borderRadius: 2 }}>
            {item.length > 45 ? item.slice(0, 45) + "…" : item}
          </span>
        ))}
        {comp.items.length > 3 && (
          <span style={{ fontSize: 10, color: "#666" }}>+{comp.items.length - 3}</span>
        )}
      </div>
    </div>
  );
}

function DependencyDiagram() {
  return (
    <div style={{ fontFamily: "'JetBrains Mono', 'SF Mono', 'Fira Code', monospace", fontSize: 11, color: "#ccc", lineHeight: 1.6, padding: "16px 0" }}>
      <pre style={{ margin: 0, whiteSpace: "pre", overflowX: "auto" }}>{`
 ┌─────────────────────────────────────────────────────────────────┐
 │                    ПЛАТФОРМА (IMMUTABLE)                         │
 │  one tool/msg · chat-based · no subagents · alwaysApply≠enforce │
 └──────────┬──────────────────────────┬───────────────────────────┘
            │ ограничивает             │ ограничивает
            ▼                          ▼
 ┌─────────────────────┐    ┌─────────────────────────────────────┐
 │   СЛОЙ 1 (STABLE)   │───▶│         СЛОЙ 2: MODES (STABLE)      │
 │  10 принципов       │    │  Architect · Code · Ask · Debug     │
 │  protocols, style   │    │  Orchestrator                       │
 └─────────┬───────────┘    └──────────┬──────────────────────────┘
           │ принципы                  │ контракты режимов
           │                           │
           │    ┌──────────────────────┐│
           │    │  WDL (EVOLVING)      ││  процесс
           │    │  methodology,        │├──создания──┐
           │    │  checklist, contract ││            │
           │    └──────────────────────┘│            │
           │                           │            │
           ▼                           ▼            ▼
 ┌─────────────────────────────────────────────────────────────────┐
 │              СЛОЙ 3: WORKFLOWS (DYNAMIC)                        │
 │                                                                 │
 │  ┌─────────┐ ┌─────────┐ ┌─────────┐ ┌─────────┐              │
 │  │  G&H    │ │ Donat.  │ │ Spons.  │ │  AKO    │  ...         │
 │  └────┬────┘ └────┬────┘ └────┬────┘ └────┬────┘              │
 │       │           │           │           │                     │
 │       └───────────┴───────────┴───────────┘                     │
 │          Изолированы друг от друга                               │
 └──────────┬──────────────────────────┬───────────────────────────┘
    Шаг 0   │                          │  KB-пути
  (чтение)  ▼                          ▼
 ┌─────────────────────┐    ┌─────────────────────────────────────┐
 │  MEMORY (DYNAMIC)   │    │     KNOWLEDGE BASE (EVOLVING)       │
 │  index · lessons    │    │  policies/ · processes/              │
 │  patterns · defs    │    │  (источник правды для контента)      │
 └─────────────────────┘    └─────────────────────────────────────┘
   ▲ обновление                 Хрупкая связь: жёсткие пути
   └── workflows`}</pre>
    </div>
  );
}

function App() {
  const [activeTab, setActiveTab] = useState("map");
  const [selectedComp, setSelectedComp] = useState(null);

  const tabs = [
    { id: "map", label: "Карта зависимостей" },
    { id: "influence", label: "Зоны влияния" },
    { id: "contract", label: "Interface Contract" },
    { id: "isolation", label: "Изоляция" },
  ];

  const comp = selectedComp ? COMPONENTS[selectedComp] : null;

  return (
    <div style={{
      minHeight: "100vh",
      background: "#0d1117",
      color: "#e6e6e6",
      fontFamily: "'Inter', -apple-system, BlinkMacSystemFont, 'Segoe UI', sans-serif",
    }}>
      {/* Header */}
      <div style={{ padding: "24px 28px 0", borderBottom: "1px solid #1e2530" }}>
        <div style={{ display: "flex", justifyContent: "space-between", alignItems: "baseline", marginBottom: 4 }}>
          <h1 style={{ margin: 0, fontSize: 18, fontWeight: 700, color: "#e6e6e6", letterSpacing: "-0.3px" }}>
            System Blueprint
          </h1>
          <span style={{ fontSize: 11, color: "#555" }}>Compliance AI Assistant · v2.6 · 2026-02-26</span>
        </div>
        <p style={{ margin: "4px 0 16px", fontSize: 12, color: "#888", maxWidth: 700 }}>
          Архитектурный чертёж: компоненты, зависимости, зоны влияния, контракты совместимости
        </p>

        {/* Tabs */}
        <div style={{ display: "flex", gap: 0 }}>
          {tabs.map(t => (
            <button
              key={t.id}
              onClick={() => setActiveTab(t.id)}
              style={{
                padding: "8px 16px",
                background: activeTab === t.id ? "#161b22" : "transparent",
                color: activeTab === t.id ? "#e6e6e6" : "#888",
                border: activeTab === t.id ? "1px solid #30363d" : "1px solid transparent",
                borderBottom: activeTab === t.id ? "1px solid #161b22" : "1px solid #1e2530",
                borderRadius: "6px 6px 0 0",
                fontSize: 12,
                fontWeight: activeTab === t.id ? 600 : 400,
                cursor: "pointer",
                marginBottom: -1,
              }}
            >
              {t.label}
            </button>
          ))}
        </div>
      </div>

      {/* Content */}
      <div style={{ padding: "20px 28px" }}>

        {/* TAB: Map */}
        {activeTab === "map" && (
          <div>
            {/* Stability legend */}
            <div style={{ display: "flex", gap: 16, marginBottom: 16, flexWrap: "wrap" }}>
              {Object.entries(STABILITY).map(([key, val]) => (
                <div key={key} style={{ display: "flex", alignItems: "center", gap: 6 }}>
                  <span style={{ width: 8, height: 8, borderRadius: 2, background: val.color }} />
                  <span style={{ fontSize: 10, color: "#888" }}><strong style={{ color: val.color }}>{val.label}</strong> — {val.desc}</span>
                </div>
              ))}
            </div>

            {/* ASCII Dependency Diagram */}
            <div style={{ background: "#161b22", border: "1px solid #30363d", borderRadius: 8, padding: "0 16px", marginBottom: 20, overflowX: "auto" }}>
              <DependencyDiagram />
            </div>

            {/* Component grid */}
            <div style={{ fontSize: 11, color: "#888", marginBottom: 8, fontWeight: 600, textTransform: "uppercase", letterSpacing: "0.5px" }}>
              Компоненты — нажми для деталей
            </div>
            <div style={{ display: "grid", gridTemplateColumns: "repeat(auto-fill, minmax(280px, 1fr))", gap: 8, marginBottom: 16 }}>
              {Object.values(COMPONENTS).map(c => (
                <ComponentCard
                  key={c.id}
                  comp={c}
                  isSelected={selectedComp === c.id}
                  onClick={() => setSelectedComp(selectedComp === c.id ? null : c.id)}
                />
              ))}
            </div>

            {/* Selected component details */}
            {comp && (
              <div style={{ background: "#161b22", border: `1px solid ${COLORS[comp.type].border}40`, borderRadius: 8, padding: 16, marginTop: 8 }}>
                <div style={{ display: "flex", justifyContent: "space-between", alignItems: "center", marginBottom: 12 }}>
                  <h3 style={{ margin: 0, fontSize: 14, color: COLORS[comp.type].text }}>{comp.name}</h3>
                  <StabilityBadge level={comp.stability} />
                </div>

                <div style={{ display: "grid", gridTemplateColumns: "1fr 1fr", gap: 16, fontSize: 12 }}>
                  <div>
                    <div style={{ color: "#888", fontSize: 10, textTransform: "uppercase", marginBottom: 4, fontWeight: 600 }}>Состав</div>
                    {comp.items.map((item, i) => (
                      <div key={i} style={{ color: "#bbb", padding: "2px 0", borderBottom: "1px solid #ffffff06" }}>• {item}</div>
                    ))}
                  </div>
                  <div>
                    <div style={{ color: "#888", fontSize: 10, textTransform: "uppercase", marginBottom: 4, fontWeight: 600 }}>Зависит от</div>
                    {comp.dependsOn.length === 0
                      ? <div style={{ color: "#2a9d8f", fontStyle: "italic" }}>Ничего — независимый компонент</div>
                      : comp.dependsOn.map((d, i) => (
                        <div key={i} style={{ color: "#bbb", padding: "2px 0" }}>↑ {COMPONENTS[d]?.name || d}</div>
                      ))
                    }

                    <div style={{ color: "#888", fontSize: 10, textTransform: "uppercase", marginBottom: 4, marginTop: 12, fontWeight: 600 }}>Влияет на</div>
                    {comp.influences.length === 0
                      ? <div style={{ color: "#2a9d8f", fontStyle: "italic" }}>Ничего — информационный слой</div>
                      : comp.influences.map((d, i) => (
                        <div key={i} style={{ color: "#bbb", padding: "2px 0" }}>↓ {COMPONENTS[d]?.name || d}</div>
                      ))
                    }

                    <div style={{ color: "#888", fontSize: 10, textTransform: "uppercase", marginBottom: 4, marginTop: 12, fontWeight: 600 }}>Радиус поломки</div>
                    <div style={{ color: "#e9c46a" }}>{comp.breakRadius}</div>

                    <div style={{ color: "#888", fontSize: 10, textTransform: "uppercase", marginBottom: 4, marginTop: 12, fontWeight: 600 }}>Контракт</div>
                    <div style={{ color: "#bbb", fontStyle: "italic" }}>{comp.contract}</div>
                  </div>
                </div>
              </div>
            )}
          </div>
        )}

        {/* TAB: Influence */}
        {activeTab === "influence" && (
          <div>
            <p style={{ fontSize: 12, color: "#888", marginTop: 0, marginBottom: 16 }}>
              Что случится, если изменить конкретный компонент? Каскадный анализ.
            </p>
            <div style={{ display: "flex", flexDirection: "column", gap: 10 }}>
              {INFLUENCE_SCENARIOS.map((s, i) => (
                <div key={i} style={{
                  background: "#161b22",
                  border: "1px solid #30363d",
                  borderRadius: 8,
                  padding: 14,
                  borderLeft: `3px solid ${s.impact === "high" ? "#e94560" : s.impact === "medium" ? "#f4a261" : "#2a9d8f"}`,
                }}>
                  <div style={{ display: "flex", justifyContent: "space-between", alignItems: "center", marginBottom: 8 }}>
                    <span style={{ fontSize: 13, fontWeight: 600, color: "#e6e6e6" }}>{s.trigger}</span>
                    <ImpactBadge level={s.impact} />
                  </div>
                  <div style={{ display: "grid", gridTemplateColumns: "1fr 1fr", gap: 12, fontSize: 12 }}>
                    <div>
                      <div style={{ color: "#888", fontSize: 10, textTransform: "uppercase", fontWeight: 600, marginBottom: 3 }}>Затронуто</div>
                      {s.affected.map((a, j) => <div key={j} style={{ color: "#bbb" }}>• {a}</div>)}
                    </div>
                    <div>
                      <div style={{ color: "#888", fontSize: 10, textTransform: "uppercase", fontWeight: 600, marginBottom: 3 }}>Действие</div>
                      <div style={{ color: "#bbb" }}>{s.action}</div>
                    </div>
                  </div>
                  <div style={{ marginTop: 8, fontSize: 11, color: "#666", fontStyle: "italic" }}>
                    Пример: {s.example}
                  </div>
                </div>
              ))}
            </div>
          </div>
        )}

        {/* TAB: Contract */}
        {activeTab === "contract" && (
          <div>
            <p style={{ fontSize: 12, color: "#888", marginTop: 0, marginBottom: 4 }}>
              Обязательный интерфейс для подключения нового workflow к системе.
            </p>
            <p style={{ fontSize: 11, color: "#666", marginTop: 0, marginBottom: 16 }}>
              Если workflow реализует этот интерфейс — он совместим. Не нужно проверять каждый mode и rule вручную.
            </p>

            <div style={{ background: "#161b22", border: "1px solid #30363d", borderRadius: 8, overflow: "hidden" }}>
              <table style={{ width: "100%", borderCollapse: "collapse", fontSize: 12 }}>
                <thead>
                  <tr style={{ background: "#1c2128" }}>
                    <th style={{ padding: "10px 14px", textAlign: "left", color: "#888", fontSize: 10, textTransform: "uppercase", fontWeight: 600, letterSpacing: "0.5px", borderBottom: "1px solid #30363d" }}>Секция</th>
                    <th style={{ padding: "10px 14px", textAlign: "left", color: "#888", fontSize: 10, textTransform: "uppercase", fontWeight: 600, letterSpacing: "0.5px", borderBottom: "1px solid #30363d" }}>Обязательные элементы</th>
                    <th style={{ padding: "10px 14px", textAlign: "center", color: "#888", fontSize: 10, textTransform: "uppercase", fontWeight: 600, letterSpacing: "0.5px", borderBottom: "1px solid #30363d", width: 30 }}>REQ</th>
                  </tr>
                </thead>
                <tbody>
                  {INTERFACE_CONTRACT.map((row, i) => (
                    <tr key={i} style={{ borderBottom: "1px solid #21262d" }}>
                      <td style={{ padding: "8px 14px", color: "#00b4d8", fontWeight: 600, verticalAlign: "top", whiteSpace: "nowrap" }}>{row.section}</td>
                      <td style={{ padding: "8px 14px", color: "#bbb" }}>
                        {row.fields.map((f, j) => <div key={j}>• {f}</div>)}
                      </td>
                      <td style={{ padding: "8px 14px", textAlign: "center", color: row.required ? "#2a9d8f" : "#555" }}>
                        {row.required ? "✓" : "○"}
                      </td>
                    </tr>
                  ))}
                </tbody>
              </table>
            </div>

            <div style={{ marginTop: 16, background: "#161b22", border: "1px solid #f4a26140", borderRadius: 8, padding: 14 }}>
              <div style={{ fontSize: 12, fontWeight: 600, color: "#f4a261", marginBottom: 6 }}>Принцип loose coupling</div>
              <div style={{ fontSize: 12, color: "#bbb", lineHeight: 1.6 }}>
                Workflow зависит от <strong style={{ color: "#00b4d8" }}>контрактов режимов</strong> (Code создаёт файл, Debug верифицирует), 
                а не от их реализации (5 фаз Code, 2 этапа Debug). Если реализация mode изменится — workflow продолжит работать, 
                пока контракт сохраняется. Аналогично: workflow ссылается на <strong style={{ color: "#2a9d8f" }}>KB по роли</strong> (
                «антикоррупционная политика»), а не только по пути — это снижает хрупкость при переименовании.
              </div>
            </div>

            <div style={{ marginTop: 10, background: "#161b22", border: "1px solid #e9456040", borderRadius: 8, padding: 14 }}>
              <div style={{ fontSize: 12, fontWeight: 600, color: "#e94560", marginBottom: 6 }}>Известные хрупкие точки</div>
              <div style={{ fontSize: 12, color: "#bbb", lineHeight: 1.6 }}>
                <div>1. <strong>KB-пути</strong> — workflows ссылаются на файлы по абсолютному пути. Контрмера: Шаг 0.5 (list_files верификация).</div>
                <div>2. <strong>Trigger overlap</strong> — нет автоматической проверки пересечений. Контрмера: deployment checklist, Блок 3c.</div>
                <div>3. <strong>Слой 1 каскад</strong> — изменение принципа затрагивает всё. Контрмера: порог в 10 принципов, объединение перед добавлением.</div>
              </div>
            </div>
          </div>
        )}

        {/* TAB: Isolation */}
        {activeTab === "isolation" && (
          <div>
            <p style={{ fontSize: 12, color: "#888", marginTop: 0, marginBottom: 16 }}>
              Какие компоненты можно менять независимо, а какие связаны каскадно.
            </p>

            <div style={{ display: "flex", flexDirection: "column", gap: 8 }}>
              {ISOLATION_PRINCIPLES.map((p, i) => (
                <div key={i} style={{
                  background: "#161b22",
                  border: "1px solid #30363d",
                  borderRadius: 8,
                  padding: 14,
                }}>
                  <div style={{ display: "flex", justifyContent: "space-between", alignItems: "center", marginBottom: 6 }}>
                    <span style={{ fontSize: 13, fontWeight: 600, color: "#e6e6e6" }}>{p.principle}</span>
                    <StatusDot status={p.status} />
                  </div>
                  <div style={{ fontSize: 12, color: "#bbb", marginBottom: p.risk ? 6 : 0 }}>
                    <strong style={{ color: "#888" }}>Механизм:</strong> {p.mechanism}
                  </div>
                  {p.risk && (
                    <div style={{ fontSize: 11, color: "#f4a261", marginTop: 4 }}>
                      ⚠ Риск: {p.risk}
                    </div>
                  )}
                </div>
              ))}
            </div>

            {/* Isolation matrix */}
            <div style={{ marginTop: 20, fontSize: 11, color: "#888", fontWeight: 600, textTransform: "uppercase", letterSpacing: "0.5px", marginBottom: 8 }}>
              Матрица изоляции: что можно менять безопасно
            </div>
            <div style={{ background: "#161b22", border: "1px solid #30363d", borderRadius: 8, overflow: "hidden" }}>
              <table style={{ width: "100%", borderCollapse: "collapse", fontSize: 11 }}>
                <thead>
                  <tr style={{ background: "#1c2128" }}>
                    <th style={{ padding: "8px 12px", textAlign: "left", color: "#888", borderBottom: "1px solid #30363d" }}>Операция</th>
                    <th style={{ padding: "8px 12px", textAlign: "center", color: "#888", borderBottom: "1px solid #30363d" }}>Безопасно?</th>
                    <th style={{ padding: "8px 12px", textAlign: "left", color: "#888", borderBottom: "1px solid #30363d" }}>Условие</th>
                  </tr>
                </thead>
                <tbody>
                  {[
                    ["Добавить workflow", "✅", "Interface contract соблюдён, triggers не пересекаются"],
                    ["Изменить содержание workflow", "✅", "Не менять triggers и routing table"],
                    ["Добавить KB-файл", "✅", "Не требует изменений в workflow"],
                    ["Переименовать KB-файл", "⚠️", "Обновить все workflow, ссылающиеся на этот файл"],
                    ["Обновить memory bank", "✅", "Информационный слой, ничего не ломает"],
                    ["Добавить принцип в Слой 1", "🔴", "Аудит всех modes + всех workflows"],
                    ["Изменить mode instructions", "⚠️", "Проверить workflows, использующие этот mode"],
                    ["Добавить rule", "✅", "Rules = visibility only, не enforcement"],
                    ["Изменить WDL-процесс", "✅", "Влияет только на создание новых workflows"],
                    ["Изменить triggers workflow", "⚠️", "Проверить пересечения (deployment checklist 3c)"],
                  ].map(([op, safe, cond], i) => (
                    <tr key={i} style={{ borderBottom: "1px solid #21262d" }}>
                      <td style={{ padding: "6px 12px", color: "#bbb" }}>{op}</td>
                      <td style={{ padding: "6px 12px", textAlign: "center", fontSize: 14 }}>{safe}</td>
                      <td style={{ padding: "6px 12px", color: "#888" }}>{cond}</td>
                    </tr>
                  ))}
                </tbody>
              </table>
            </div>
          </div>
        )}
      </div>
    </div>
  );
}

export default App;