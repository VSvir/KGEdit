# KGEdit: *Обновление знаний в LLM с использованием графов знаний*

**KGEdit** — метод обновления знаний в больших языковых моделях (LLM) без изменения их параметров, основанный на использовании графов знаний. Данный метод разработан в рамках курсовой работы. Репозиторий содержит реализацию метода и код экспериментов.

## Ключевые особенности метода
- **Гибридное хранение знаний**:
  - Кастомный граф — используется для хранения *обновлённых фактов* (триплеты `субъект-отношение-объект`).
  - Wikidata — источник достоверных *базовых знаний* (для доступа применяются SPARQL-запросы).
- **Безопасность архитектуры**: Не изменяет параметры LLM, что:
  - Исключает риск деградации модели.
  - Позволяет работать с LLM закрытой архитектуры (ChatGPT, Claude и др.).
- **Эффективность ресурсов**:
  - Низкие вычислительные затраты (отсутствие тонкой настройки LLM).
  - Компактное хранение правок в структуре графа.
- **Верификация ответов**: Автоматическая проверка корректности применения правок с помощью NLI-модели (RoBERTa large MNLI).

## Алгоритм работы
1. **Подготовка**: LLM преобразует новые факты в триплеты → сохранение в кастомный граф.
2. **Обработка запроса**:
   - Извлечение из запроса пары `субъект-отношение`.
   - Поиск в кастомном графе → при отсутствии подходящей информации поиск в Wikidata.
3. **Генерация**: LLM создаёт ответ с учётом найденных фактов.
4. **Проверка**: NLI-модель контролирует соответствие ответа обновлённым знаниям → при конфликте ответ корректируется.

## Результаты экспериментов
Метод тестировался на выборках $\text{WikiData}_\text{Recent}$ с моделями Qwen2.5 (1.5B/3B-instruct) и сравнивался с подходами ICE, LoRA, MELO, FT.

|        Метрика        | Результаты KGEdit |                         Сравнение с другими подходами                         |
|:---------------------:|:-----------------:|-------------------------------------------------------------------------------|
| **Успешность правок** |       60–84%      |                          Работает наравне с аналогами                         |
|    **Локальность**    |     **73–83%**    | **Лучший результат после MELO (100%)**, существенно превосходит другие методы |
|   **Переносимость**   |       15–35%      |              **Основной недостаток**: уступает аналогам (26–75%)              |

### **Ключевые выводы**
- **Сильные стороны**: Высокая локальность (точечное применение правок), низкие ресурсные затраты.
- **Ограничения**: Низкая переносимость правок на смежные факты, слабая обработка сложных запросов.
- **Зависимость от модели**: Эффективен только с *instruction-tuned* LLM (на базовых Qwen качество значительно деградирует).

## Итоги
+ Метод **KGEdit** демонстрирует **эффективность** в безопасном обновлении знаний LLM без редактирования параметров.
+ **Направления для улучшений**:
   - Оптимизация обработки сложных запросов (декомпозиция вопросов).
   - Улучшение работы с графом знаний.
   - Адаптация под базовые LLM.
