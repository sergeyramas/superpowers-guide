# 🦸 Superpowers — гайд на русском

> **Полная методология разработки** для AI-агентов (Claude Code, Codex, Gemini CLI). 14 поведенческих skills, которые превращают агента из «болтливой генерилки кода» в дисциплинированного инженера.
> Это **гайд** на русском с инструкцией как установить и использовать. Сам пакет — open-source MIT от [obra/superpowers-marketplace](https://github.com/obra/superpowers-marketplace) (Jesse Vincent).

---

## 🎯 TL;DR

**Проблема:** обычный Claude Code (и любой другой агент) при запросе «сделай X» сразу пишет код. Без брейнсторма, без плана, без TDD, без верификации. Результат — недоделки, лишний код, регрессии.

**Решение:** Superpowers — это набор из 14 skills, которые **триггерятся автоматически** на разные фазы работы и заставляют агента вести себя как опытный инженер:

```
Запрос → brainstorming → writing-plans → TDD → executing-plans
       → verification-before-completion → requesting-code-review
       → finishing-a-development-branch → done
```

После установки **ничего настраивать не нужно** — agent сам подхватит skill в нужный момент.

---

## 💡 Зачем это бизнесу / разработчику

| До Superpowers | После Superpowers |
|---|---|
| «сделай фичу» → агент сразу кодит | «сделай фичу» → агент сначала уточняет, что именно ты хочешь |
| Спека формируется в голове, а не на бумаге | Чёткая спека, разбитая на читаемые куски, согласовывается с тобой |
| Тестов либо нет, либо они написаны после фичи | Жёсткая red/green TDD — тест **обязан** упасть до начала имплементации |
| Агент говорит «готово» без проверки | `verification-before-completion` требует доказательств |
| После ревью агент покорно соглашается со всеми правками | `receiving-code-review` требует технического обоснования каждой правки |
| 1 задача = 1 длинная последовательная сессия | Subagent-driven development — параллельные ветки на независимые задачи |

В реальной работе это означает **несколько часов автономной работы агента** без отклонения от плана. Не идеально, но в разы лучше, чем default-режим.

---

## 📚 Что внутри: 14 skills

### Process / Discovery

| Skill | Когда срабатывает |
|---|---|
| `using-superpowers` | **Авто на старте сессии**. Учит агента, как искать и применять остальные skills. Бутстрап-слой. |
| `brainstorming` | **Перед** любой creative-работой (новые фичи, компоненты, поведения). Исследует, что ты на самом деле хочешь, прежде чем писать код. |
| `writing-plans` | Спека есть → нужен пошаговый имплементационный план. Декомпозиция, чтобы её мог выполнить «энтузиастный junior без вкуса и контекста». |

### Execution

| Skill | Когда срабатывает |
|---|---|
| `executing-plans` | План написан → выполнить в **отдельной** сессии с review-чекпоинтами. |
| `subagent-driven-development` | План написан → выполнить в **текущей** сессии через сабагентов. |
| `dispatching-parallel-agents` | 2+ независимые задачи без shared state → запустить параллельно вместо последовательно. |
| `using-git-worktrees` | Нужна изоляция feature-работы от текущего workspace → создать git-worktree с safety-проверками. |

### Quality

| Skill | Когда срабатывает |
|---|---|
| `test-driven-development` | Любая фича / багфикс → сначала падающий тест, потом код. Жёстко red/green. |
| `systematic-debugging` | Баг / падающий тест / неожиданное поведение → системный подход вместо гадания. |
| `verification-before-completion` | **Перед** заявлением «готово / fixed / passing» → требует прогнать команды верификации и подтвердить вывод. Evidence before assertions. |

### Review & Ship

| Skill | Когда срабатывает |
|---|---|
| `requesting-code-review` | Завершил задачу / большую фичу → запросить ревью перед merge. |
| `receiving-code-review` | Получил фидбек → как его обрабатывать без performative agreement. Технический rigor, верификация каждой правки. |
| `finishing-a-development-branch` | Имплементация готова, тесты зелёные → как мержить / делать PR / чистить. |
| `writing-skills` | Создание / редактирование самих skills + проверка перед deploy. |

---

## ⚙️ Установка

### Вариант 1 — Официальный marketplace Anthropic (рекомендую)

```bash
# В Claude Code:
/plugin install superpowers@claude-plugins-official
```

Это самый стабильный путь — pakage прошёл review Anthropic.

### Вариант 2 — Marketplace автора (свежие фичи раньше)

```bash
# В Claude Code:
/plugin marketplace add obra/superpowers-marketplace
/plugin install superpowers@superpowers-marketplace
```

Используется, если хочешь свежие фичи до их попадания в official marketplace.

### Вариант 3 — OpenAI Codex CLI

```bash
# В Codex:
> /plugins
# Найти superpowers, установить
```

### Вариант 4 — Gemini CLI

См. оригинальный README — там есть `gemini-extension.json`.

---

## 🚀 Типовые сценарии использования

### A. «Сделай мне новый landing page»

```
ВЫ:    Сделай мне landing page для нашего продукта X.

CLAUDE (с Superpowers):
       [триггерится brainstorming]
       Прежде чем писать код, давай разберёмся:
       1. Кто целевая аудитория?
       2. Какая главная боль?
       3. Какие 3 ключевых преимущества?
       4. Что должно произойти после клика на CTA?
       ...

ВЫ:    [отвечаешь]

CLAUDE: [триггерится writing-plans]
       Окей, вот план в 5 шагов:
       1. Hero: пейн → решение → primary CTA
       2. Section: 3 преимущества с метриками
       3. ...
       Согласен начать с шага 1?

ВЫ:    Да.

CLAUDE: [триггерится TDD + executing-plans]
       Пишу падающий тест для CTA-ссылки. Запускаю — failed (ожидаемо).
       Имплементирую CTA-компонент. Тест зелёный.
       [триггерится verification-before-completion]
       Прогоняю build, lint, тесты — всё ОК. Шаг 1 готов.
```

### B. «У меня баг в проде»

```
ВЫ:    Кнопка submit не работает в Safari.

CLAUDE: [триггерится systematic-debugging]
       Прежде чем гадать — соберу факты:
       1. В каких версиях Safari? Mobile/Desktop?
       2. Воспроизводится локально?
       3. Что в console?
       4. Какой последний коммит трогал submit?
       Дай мне эту информацию — потом сформулирую гипотезу.
```

### C. «Реализуй эти 4 фичи параллельно»

```
ВЫ:    Нужно: 1) добавить dark mode, 2) переделать footer,
       3) починить SEO-теги, 4) добавить sitemap.

CLAUDE: [триггерится dispatching-parallel-agents]
       Эти 4 задачи независимы (нет shared state).
       Запускаю 4 sub-agent'а параллельно, каждый в своём worktree.
       Через ~15 минут получишь 4 готовых PR на ревью.
```

---

## ❓ FAQ

**Q: Это правда работает?**
A: Да. Автор (Jesse Vincent) использует это в продакшне. У меня в personal-проектах — Superpowers + кастомные skills экономят 30-50% времени за счёт меньшего числа итераций.

**Q: Сжигает токены сильнее?**
A: Чуть-чуть на старт (системный промпт растёт на ~5-10K токенов) и на brainstorming-фазу. Но компенсируется за счёт меньшего числа неудачных попыток и переделок.

**Q: Совместимо с моими собственными skills?**
A: Полностью. Superpowers — отдельный плагин с префиксом `superpowers:` (например, `superpowers:brainstorming`). Не конфликтует с твоими skills в `~/.claude/skills/`.

**Q: Можно ли отключить отдельные skills?**
A: Да. Каждый skill — отдельный `SKILL.md`. Удалить из `~/.claude/plugins/marketplaces/superpowers-dev/skills/` (или скопировать локально и редактировать).

**Q: Это open-source?**
A: Полностью. **MIT License.** Можно форкать, модифицировать, использовать коммерчески.

**Q: А если я не хочу `brainstorming` на каждый запрос?**
A: skill срабатывает только на **creative work** (новые фичи / компоненты). Простые правки — не триггерятся. Если всё-таки надоело — отредактируй описание `brainstorming/SKILL.md` чтобы триггер был уже.

**Q: Почему называется «Superpowers»?**
A: Marketing :) По сути — это **дисциплина**, без которой LLM'ы по умолчанию не работают. После установки агент перестаёт быть junior'ом.

---

## 🔗 Ссылки

- **Upstream:** https://github.com/obra/superpowers-marketplace
- **Автор:** [Jesse Vincent (@obra)](https://github.com/obra) — поддержать через [GitHub Sponsors](https://github.com/sponsors/obra)
- **Этот гайд:** https://github.com/sergeyramas/superpowers-guide
- **Хаб с другими решениями:** https://sergeyramas.vercel.app

---

## 🤝 Кредиты

Этот гайд написан **Сергеем Рамасом** (sergeyramas) и не является официальным проектом Superpowers.

Сам Superpowers создан **Jesse Vincent (obra)** и распространяется под MIT-лицензией. Если экосистема помогает тебе зарабатывать — [поддержи автора](https://github.com/sponsors/obra).

---

**License:** MIT (этот гайд)
**Upstream license:** MIT (sам пакет Superpowers)
