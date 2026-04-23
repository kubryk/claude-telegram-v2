# claude-telegram-v2

Telegram-канал для Claude Code з підтримкою локального Bot API — дозволяє отримувати та надсилати файли до **2 GB** (стандартний Telegram API обмежений 20 MB).

## Структура

```
claude-telegram-v2/
├── plugin/            # Claude Code плагін (MCP-сервер)
├── docker-compose.yml # Локальний Telegram Bot API
├── .env.example       # Шаблон змінних середовища
└── README.md
```

## Передумови

- [Docker](https://docker.com) — для локального Telegram Bot API
- [Bun](https://bun.sh) — для запуску MCP-сервера (`curl -fsSL https://bun.sh/install | bash`)
- [Claude Code](https://claude.ai/code) — CLI

## Встановлення

### 1. Клонувати репозиторій

```bash
git clone https://github.com/kubryk/claude-telegram-v2.git
cd claude-telegram-v2
```

### 2. Заповнити змінні середовища

```bash
cp .env.example .env
```

Відкрий `.env` і заповни:

| Змінна | Де отримати |
|--------|-------------|
| `TELEGRAM_API_ID` | https://my.telegram.org/apps |
| `TELEGRAM_API_HASH` | https://my.telegram.org/apps |
| `TELEGRAM_BOT_TOKEN` | [@BotFather](https://t.me/BotFather) → `/newbot` |

### 3. Запустити локальний Telegram Bot API

```bash
docker-compose up -d
```

Перевірка: `curl http://localhost:8081/` — має повернути `{"ok":true}`.

### 4. Встановити плагін у Claude Code

```bash
claude plugin marketplace add github:kubryk/claude-telegram-v2 --sparse plugin
claude plugin install telegram-local@claude-telegram-v2
```

### 5. Налаштувати бота

Запусти Claude Code і виконай:

```
/telegram-local:configure <TELEGRAM_BOT_TOKEN>
```

### 6. Запустити з каналом

```bash
claude --channels plugin:telegram-local@claude-telegram-v2
```

### 7. Спарити пристрій

Напиши будь-яке повідомлення своєму боту в Telegram — він відповість 6-значним кодом. Потім у Claude Code:

```
/telegram-local:access pair <код>
```

Після першого входу переключи політику на allowlist:

```
/telegram-local:access policy allowlist
```

## Локальний Bot API vs хмарний

| | Хмарний API | Локальний API (цей репо) |
|---|---|---|
| Макс. розмір файлу | 20 MB | **2 GB** |
| Швидкість | стандартна | вища (прямий сервер) |
| Налаштування | нічого | Docker |

## Інструменти плагіна

| Інструмент | Призначення |
|------------|-------------|
| `reply` | Відправити повідомлення (текст + файли) |
| `react` | Додати emoji-реакцію |
| `edit_message` | Тихо оновити попереднє повідомлення |
| `download_attachment` | Завантажити файл із Telegram (до 2 GB) |

Детальніше про права доступу: [plugin/ACCESS.md](./plugin/ACCESS.md)
