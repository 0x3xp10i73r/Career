# Lab: DOM XSS in innerHTML sink using source location.search

This lab contains a DOM-based cross-site scripting vulnerability in the search blog functionality. It uses an innerHTML assignment, which changes the HTML contents of a div element, using data from location.search.

To solve this lab, perform a cross-site scripting attack that calls the alert function.

# Source & Sink in Javascript

## Source

### Definition

A Source is any place where data originates. This data often comes from users, browsers, or external systems, and should be treated as untrusted unless validated.

### Common JavaScript Sources

```jsx
prompt("Enter value");          // User input
location.href                  // URL
document.cookie                // Cookies
localStorage.getItem("key")     // Browser storage

```

---

## Sink

### Definition

A **Sink** is any place where data is **used, executed, or displayed**.

Improper handling of data at sinks can cause **security vulnerabilities**.

### Common JavaScript Sinks

```jsx
document.write(data);
element.innerHTML = data;
eval(data);
console.log(data);

```

---

## Data Flow Example

```jsx
let userInput = prompt("Enter your name"); // Source
document.getElementById("output").innerHTML = userInput; // Sink

```

- `prompt()` → Source
- `innerHTML` → Sink

---

- The code reads user input from the URL using `window.location.search`
- `URLSearchParams.get('search')` extracts the `search` parameter
- URL parameters are **user-controlled** and must be treated as **untrusted input**
- The extracted value is passed to the function `doSearchQuery(query)`
- Inside the function, the value is assigned to `innerHTML`
- `innerHTML` is a **dangerous sink** because it parses HTML
- If the input contains HTML or JavaScript, the browser will execute it
- No validation or sanitization is applied to the input
- This creates a **DOM-based XSS vulnerability**

```jsx
function doSearchQuery(query) {
    document.getElementById('searchMessage').innerHTML = query;
}

var query = (new URLSearchParams(window.location.search)).get('search');

if (query) {
    doSearchQuery(query);
}
/*
location.search (Source)
        ↓
URLSearchParams.get('search')
        ↓
doSearchQuery(query)
        ↓
innerHTML (Sink)
*/
```

1. Visit the Webpage 

![image.png](Lab%20DOM%20XSS%20in%20innerHTML%20sink%20using%20source%20locatio/image.png)

![image.png](Lab%20DOM%20XSS%20in%20innerHTML%20sink%20using%20source%20locatio/image%201.png)

![image.png](Lab%20DOM%20XSS%20in%20innerHTML%20sink%20using%20source%20locatio/image%202.png)

![image.png](Lab%20DOM%20XSS%20in%20innerHTML%20sink%20using%20source%20locatio/image%203.png)