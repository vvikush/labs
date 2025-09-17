## Авто-черновики ответов для Gmail (на основе базы знаний)

Этот проект создаёт черновики ответов в Gmail на входящие письма, используя JSON-базу знаний, набор правил и шаблон. Работает без LLM по умолчанию, при желании можно включить OpenAI для переформулирования.

### Структура
- `data/knowledge_base.json` — база знаний (пример включён)
- `data/rules.json` — правила ответа и подпись (пример включён)
- `prompts/reply_template.j2` — шаблон финального ответа
- `src/autoreply/` — код: конфиг, Gmail-клиент, поиск по БЗ, генератор ответа, CLI

### Быстрый старт
1) Установить зависимости
```bash
python3 -m venv .venv && source .venv/bin/activate
pip install -r requirements.txt
```

2) Настроить переменные окружения
```bash
cp .env.example .env
# заполните GOOGLE_CLIENT_ID, GOOGLE_CLIENT_SECRET и GOOGLE_REFRESH_TOKEN
# при необходимости измените пути к БЗ/правилам/шаблонам
```

3) Получить refresh token Gmail (однократно)
- Включите Gmail API в Google Cloud
- Создайте OAuth 2.0 Client ID типа "Desktop application"
- Затем запустите помощник (откроет браузер):
```bash
PYTHONPATH=src python tools/get_refresh_token.py
```
Скопируйте выведенный `refresh_token` в `.env`.

4) Создать черновик ответа на самое новое непрочитанное письмо
```bash
PYTHONPATH=src python -m autoreply.cli draft-latest --query "is:unread -category:promotions -category:social"
```
Вывод покажет `draft_id` и тему письма.

5) Опционально: включить OpenAI для переформулирования
- Установите `OPENAI_API_KEY` и `USE_OPENAI=true` в `.env`.

### Формат базы знаний
`data/knowledge_base.json` — массив объектов
```json
[
  {
    "id": "policies-pricing",
    "title": "Цены и биллинг",
    "content": "Подписка Pro стоит 20 USD/мес. Доступна годовая скидка 20%.",
    "tags": ["billing", "pricing"]
  }
]
```

### Команды CLI
- Создать черновик на последнее непрочитанное: 
```bash
PYTHONPATH=src python -m autoreply.cli draft-latest
```
- Сгенерировать ответ из текстового файла (без Gmail):
```bash
PYTHONPATH=src python -m autoreply.cli generate --subject "Вопрос по тарифам" --input ./email.txt
```

### Безопасность
- Храните `.env` в секрете.
- Refresh token можно отозвать в Google Cloud.

### Лицензия
MIT