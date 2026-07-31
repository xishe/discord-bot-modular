# majestic_bot2

Модульная версия Discord-бота для проекта **Majestic RP**, написанная на [disnake](https://github.com/DisnakeDev/disnake). В отличие от [majestic_bot](https://github.com/xishe/majestic_bot), здесь функционал вынесен из одного файла в отдельные модули (`cogs`), что упрощает поддержку и добавление новых команд.

## Структура проекта

```
majestic_bot2/
├── main.py            # точка входа: загрузка конфига и когов, запуск бота
├── config.json         # конфигурация бота (префикс команд и токен)
├── cogs/                # модули (коги) с командами и логикой бота
├── data/                # хранимые данные бота (JSON и т.п.)
├── prosecutors.json     # список курирующих прокуроров по подразделениям
├── gov_last.conf        # supervisor-конфиг для запуска бота как демона на сервере
└── README.md
```

## Как это работает

- `main.py` читает `config.json`, создаёт `commands.Bot` с указанным префиксом и токеном, затем автоматически подгружает **все** `.py`-файлы из папки `cogs/` как расширения (`bot.load_extension`).
- Каждый ког в `cogs/` реализует свою часть функционала бота (например, работу со списками сотрудников ведомств, курирующими прокурорами и другие команды фракции) — модули можно включать/отключать независимо, просто добавляя или убирая файл из `cogs/`.
- `prosecutors.json` и файлы в `data/` используются когами как хранилище состояния (списки, ID и т.п.) между перезапусками бота.
- `gov_last.conf` — готовый конфиг для [Supervisor](http://supervisord.org/), позволяющий держать бота постоянно запущенным на сервере с авто-рестартом при падении.

## Стек

- Python 3
- [disnake](https://github.com/DisnakeDev/disnake) — обёртка над Discord API
- Хранение данных — JSON-файлы (`config.json`, `prosecutors.json`, `data/`)
- [Supervisor](http://supervisord.org/) — для деплоя как системного демона (опционально)

## Установка

```bash
git clone https://github.com/xishe/majestic_bot2.git
cd majestic_bot2
pip install disnake
```

## Настройка

Отредактируйте `config.json`:

```json
{
  "prefix": ".",
  "token": "token-bot"
}
```

- `prefix` — префикс текстовых команд бота.
- `token` — токен вашего Discord-бота (получить можно в [Discord Developer Portal](https://discord.com/developers/applications)).

Также проверьте коги в папке `cogs/` — многие из них могут содержать захардкоженные ID сервера, каналов и ролей под конкретный сервер Majestic RP, которые нужно заменить на свои при переносе на другой сервер.

## Запуск

```bash
python main.py
```

### Запуск как демон (Linux, через Supervisor)

Если нужно, чтобы бот работал постоянно и автоматически перезапускался при сбоях, используйте `gov_last.conf`:

```bash
cp gov_last.conf /etc/supervisor/conf.d/
supervisorctl reread
supervisorctl update
supervisorctl start gov_last
```

> В конфиге путь к проекту указан как `/root/gov_last/` — при необходимости поправьте под своё расположение файлов.

## Автор

by x1she
