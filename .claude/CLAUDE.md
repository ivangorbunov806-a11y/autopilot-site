# AI Brand Engine — инструкции для Claude Code

## Что за проект

Сайт-демо AI-специалиста по автоматизации. Живой сайт: автопилот24.рф
Главная фишка — AI-ассистент прямо на странице (GigaChat API).

## Структура

```
public/          — все HTML-файлы и медиа (то что видит браузер)
server.js        — Node.js сервер, порт 8080, читает .env
.env             — GIGA_AUTH ключ (не в репо, создать из .env.example)
```

## Как запустить локально

```bash
cp .env.example .env   # добавить GIGA_AUTH
node server.js         # http://localhost:8080
```

На проде: PM2 + Nginx. SSH: `ssh autopilot`
Файлы на сервере: `/var/www/autopilot/public/`

## Как вносить правки

**Правки HTML** — редактировать файлы в `public/`, затем загрузить на сервер:
```bash
scp public/index.html autopilot:/var/www/autopilot/public/
```

**Правки server.js** — после изменений перезапустить:
```bash
scp server.js autopilot:/var/www/autopilot/
ssh autopilot "pm2 restart autopilot"
```

**Важно:** правки с кириллицей делать через Python-скрипт по SSH pipe, не через bash heredoc:
```bash
ssh autopilot 'python3 -' << 'PYEOF'
# python код здесь
PYEOF
```

## Ключевые части кода

- `public/index.html:1484` — SYSTEM_PROMPT бота (на сервере, не в браузере)
- `server.js` — `/api/chat` endpoint, rate limiting, injection protection
- `public/index.html:1530` — функция `formatBot()` — рендеринг ответов бота

## Добавить новую статью в блог

1. Скопировать `public/blog-template.html`
2. Заменить плейсхолдеры: `{{TITLE}}`, `{{SLUG}}`, `{{DATE_ISO}}`, `{{IMAGE}}`, `{{ALT}}`, `{{CATEGORY}}`, `{{DATE_DISPLAY}}`, `{{READ_TIME}}`, `{{LEAD}}`, `{{CONTENT}}`, `{{CTA_TITLE}}`, `{{CTA_TEXT}}`
3. Добавить карточку в `public/blog.html` и в блок блога на `public/index.html`

## Проверить ошибки на сервере

```bash
ssh autopilot "pm2 logs autopilot --lines 20 --nostream"
```
