# HTTP Headers Cheat Sheet (Pentest)

## Identity (Client)

| Header | Що робить | Де використовується | Атака | Приклад | Що зміниться при успіху |
|--------|-----------|---------------------|--------|---------|--------------------------|
| X-Forwarded-For (IP клієнта) | Підміняє IP | Логи, auth, rate limit | Rate limit bypass | X-Forwarded-For: 127.0.0.1 | Сервер думає, що ти інший користувач |
| X-Real-IP (реальний IP) | Підміняє IP | Backend (nginx, apache) | IP spoofing | X-Real-IP: 127.0.0.1 | IP в логах / перевірках змінюється |
| Forwarded (for=) | Підміняє IP | Proxy / modern apps | Bypass / spoofing | Forwarded: for=127.0.0.1 | Те саме, але через стандарт |

---

## Host / Domain

| Header | Що робить | Де використовується | Атака | Приклад | Що зміниться при успіху |
|--------|-----------|---------------------|--------|---------|--------------------------|
| Host (домен) | Визначає домен | Генерація URL | Host header attack | Host: evil.com | Лінки / редіректи ведуть на твій домен |
| X-Forwarded-Host (оригінальний домен) | Підміняє домен для backend | Middleware / proxy | Password reset poisoning | X-Forwarded-Host: exploit.com | Reset link містить твій домен |
| X-Forwarded-Server (сервер) | Підміняє сервер | Backend логіка | URL manipulation | X-Forwarded-Server: evil.com | URL або логіка будується інакше |
| Forwarded (host=) | Підміняє домен | Proxy | Poisoning | Forwarded: host=evil.com | Те саме через стандарт |

---

## Protocol

| Header | Що робить | Де використовується | Атака | Приклад | Що зміниться при успіху |
|--------|-----------|---------------------|--------|---------|--------------------------|
| X-Forwarded-Proto (http/https) | Підміняє протокол | Генерація URL | Downgrade / manipulation | X-Forwarded-Proto: http | HTTPS → HTTP у лінках |
| Forwarded (proto=) | Підміняє протокол | Proxy | URL manipulation | Forwarded: proto=http | Те саме через стандарт |

---

## Port

| Header | Що робить | Де використовується | Атака | Приклад | Що зміниться при успіху |
|--------|-----------|---------------------|--------|---------|--------------------------|
| X-Forwarded-Port (порт) | Підміняє порт | Backend | URL confusion | X-Forwarded-Port: 8080 | Лінки містять інший порт |
| Host (домен + порт) | Домен + порт | URL | Injection | Host: evil.com:8080 | URL змінюється |

---

## Path / Rewrite

| Header | Що робить | Де використовується | Атака | Приклад | Що зміниться при успіху |
|--------|-----------|---------------------|--------|---------|--------------------------|
| X-Original-URL (оригінальний шлях) | Підміняє шлях | IIS / proxy | Access control bypass | X-Original-URL: /admin | Отримуєш доступ до закритого endpoint |
| X-Rewrite-URL (перезаписаний шлях) | Перезаписує шлях | Routing | Path manipulation | X-Rewrite-URL: /admin | Сервер обробляє інший шлях |

---

## Common Attack Mapping

| Атака | Header | Результат |
|------|--------|-----------|
| Password reset poisoning | X-Forwarded-Host | Контроль над reset link |
| Host header attack | Host | Контроль домену в URL |
| Rate limit bypass | X-Forwarded-For | Обхід обмежень |
| URL manipulation | X-Forwarded-Proto | Зміна схеми |
| Access control bypass | X-Original-URL | Обхід доступу |

---

## Example Requests

### Password Reset Poisoning

POST /forgot-password HTTP/1.1  
Host: target.com  
X-Forwarded-Host: exploit-server.com  

---

### Rate Limit Bypass

POST /login HTTP/1.1  
Host: target.com  
X-Forwarded-For: 127.0.0.1  

---

### Protocol Manipulation

GET / HTTP/1.1  
Host: target.com  
X-Forwarded-Proto: http  

---
