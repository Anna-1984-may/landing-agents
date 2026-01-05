# Процесс разработки лендинга с ИИ-агентами

Ниже описан воспроизводимый процесс с четкими входами и выходами. Все результаты работы
агентов фиксируются в `artifacts/` и имеют единый формат (json/md). Это позволяет
возвращаться на шаг назад, править артефакт и продолжать без потери контекста.

## Как пользоваться агентами (пошагово)
1. Сложите все исходные материалы в `context/raw/`.
2. Убедитесь, что в `templates/` есть все шаблоны артефактов.
3. Если это повторная итерация, заполните `artifacts/iteration_notes.md` по шаблону `templates/iteration_notes.md`.
4. Для каждого шага:
   - добавьте в контекст агента файл его роли из `roles/`;
   - добавьте `artifacts/project_summary.md` (источник истины) и `artifacts/iteration_notes.md` (если есть);
   - добавьте все входные артефакты и нужные справочные файлы из `guides/`;
   - запустите агента и сохраните результат в `artifacts/`.
4. Если агент сообщает о нехватке данных, ответьте на вопросы из `artifacts/questions.md`
   и дополните `context/raw/` или `artifacts/brief.json`, затем повторите шаг.

## Общие правила для всех агентов
- Не придумывать факты. Если данных нет — оставлять поля пустыми и задавать вопросы.
- Не менять структуру шаблонов. Никаких новых полей и форматов.
- JSON должен быть валидным. Никаких комментариев и лишнего текста.
- Менять можно только целевые артефакты своей роли.
- Любая правка делается на предыдущем шаге, дальше шаги повторяются по цепочке.
- `artifacts/project_summary.md` — источник истины. Если другие артефакты противоречат ему, фиксируй вопрос.

## Термины (кратко)
- Super Big Job: жизненная цель пользователя.
- Big Job: конкретная жизненная задача.
- Core Job: ключевая работа, которую выполняет продукт.
- Micro Job: маленький шаг пользователя (CTA).
- Oneliner: что это + какую Core Job выполняет + с какой ценностью.

## Шаги и артефакты (детально)
Во всех шагах используй `artifacts/project_summary.md` и `artifacts/iteration_notes.md` (если есть).

1) Сбор контекста
- Цель: превратить хаотичный контекст в единый бриф.
- Вход: `context/raw/`
- Роль: Экстрактор (`roles/extractor.md`)
- Выход: `artifacts/brief.json`, `artifacts/project_summary.md`, `artifacts/questions.md`
- Контроль: все ключевые поля заполнены или явно пустые + вопросы сформулированы.
- Короткий промпт запуска: прочитай `roles/extractor.md` и выполни инструкции из него; заполни `artifacts/brief.json` и `artifacts/project_summary.md`.

2) Jobs-карта
- Цель: описать Jobs-иерархию и сегменты.
- Вход: `artifacts/brief.json`
- Роль: Стратег (`roles/strategist.md`)
- Выход: `artifacts/jobs.json`, `artifacts/jobs.md`
- Контроль: есть Super Big Job, Big Job, список Core Jobs, Micro Jobs и сегменты.
- Короткий промпт запуска: прочитай `roles/strategist.md` и выполни инструкции из него; заполни `artifacts/jobs.json` и `artifacts/jobs.md`.

3) Ценности и oneliner
- Цель: сформулировать проверяемые ценности и базовый смысл.
- Вход: `artifacts/brief.json`, `artifacts/jobs.json`
- Роль: Стратег позиционирования (`roles/positioning.md`)
- Выход: `artifacts/value_props.json`, `artifacts/oneliner.md`, `artifacts/cta.md`
- Контроль: нет абстрактных ценностей без расшифровки.
- Короткий промпт запуска: прочитай `roles/positioning.md` и выполни инструкции из него; заполни `artifacts/value_props.json`, `artifacts/oneliner.md`, `artifacts/cta.md`.

4) Сценарий лендинга (структура блоков)
- Цель: выстроить путь от точки А к точке Б через последовательность блоков.
- Вход: `artifacts/jobs.json`, `artifacts/value_props.json`, `artifacts/oneliner.md`
- Роль: Архитектор сценария (`roles/architect.md`)
- Выход: `artifacts/structure.json`, `artifacts/structure.md`
- Контроль: 1 блок = 1 смысл, присутствуют блоки контекста, барьеров и доверия.
- Короткий промпт запуска: прочитай `roles/architect.md` и выполни инструкции из него; заполни `artifacts/structure.json` и `artifacts/structure.md`.

5) Тексты блоков
- Цель: написать короткие, ясные тексты по структуре.
- Вход: `artifacts/structure.json`, `artifacts/value_props.json`
- Роль: Копирайтер (`roles/copywriter.md`)
- Выход: `artifacts/copy.json`
- Контроль: заголовки читаются за 6 секунд, CTA соответствует Micro Job.
- Короткий промпт запуска: прочитай `roles/copywriter.md` и выполни инструкции из него; заполни `artifacts/copy.json`.

6) Визуальные контексты и промпты
- Цель: описать визуал, который усиливает смысл блоков.
- Вход: `artifacts/structure.json`, `artifacts/copy.json`
- Роль: Визуальный агент (`roles/visual.md`)
- Выход: `artifacts/visuals.json`
- Контроль: визуал показывает точку Б и не противоречит текстам.
- Короткий промпт запуска: прочитай `roles/visual.md` и выполни инструкции из него; заполни `artifacts/visuals.json`.

6.1) Генерация изображений (опционально)
- Цель: создать реальные изображения по промптам.
- Вход: `artifacts/visuals.json`
- Роль: Генератор изображений (`roles/image_generator.md`)
- Выход: `landing/assets/`, `artifacts/image_manifest.md`
- Контроль: имена файлов совпадают с `visuals.json`.
- Короткий промпт запуска: прочитай `roles/image_generator.md` и выполни инструкции из него; создай изображения и заполни `artifacts/image_manifest.md`.

7) Прототип/вайрфрейм
- Цель: определить структуру и приоритеты без дизайна.
- Вход: `artifacts/structure.json`, `artifacts/copy.json`, `artifacts/visuals.json`
- Роль: UX агент (`roles/ux.md`)
- Выход: `artifacts/wireframe.md`
- Контроль: у каждого блока указан главный акцент и место CTA.
- Короткий промпт запуска: прочитай `roles/ux.md` и выполни инструкции из него; заполни `artifacts/wireframe.md`.

8) Верстка
- Цель: собрать лендинг по утвержденной структуре.
- Вход: `artifacts/structure.json`, `artifacts/copy.json`, `artifacts/visuals.json`, `artifacts/wireframe.md`
- Роль: Верстальщик (`roles/frontend.md`)
- Выход: `landing/`
- Контроль: тексты, блоки и CTA строго соответствуют артефактам.
- Короткий промпт запуска: прочитай `roles/frontend.md` и выполни инструкции из него; собери `landing/`.

9) QA и ревизии
- Цель: проверить качество и соответствие Jobs-логике.
- Вход: все артефакты и `landing/`
- Роль: QA агент (`roles/qa.md`)
- Выход: `artifacts/qa.md`
- Контроль: есть список замечаний и указание, на каком шаге править.
- Короткий промпт запуска: прочитай `roles/qa.md` и выполни инструкции из него; заполни `artifacts/qa.md`.

10) Ретроспектива процесса (опционально)
- Цель: улучшить промпты, роли и workflow по итогам итерации.
- Вход: `WORKFLOW.md`, `roles/*`, `artifacts/*`, `landing/` (если есть)
- Роль: Агент обратной связи (`roles/feedback.md`)
- Выход: `artifacts/process_feedback.md`
- Короткий промпт запуска: прочитай `roles/feedback.md` и выполни инструкции из него; заполни `artifacts/process_feedback.md`.
