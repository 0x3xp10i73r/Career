# Lab: DOM XSS in document.write sink using source location.search

- Source and Sink in JavaScript
    
    # Source & Sink in JavaScript
    
    ## Overview
    
    In JavaScript, **Source** and **Sink** describe how data **enters** and **leaves** a program. These concepts are especially important in **data flow analysis** and **web security**.
    
    ---
    
    ## Source
    
    ### Definition
    
    A **Source** is any place where data **originates**.
    
    This data often comes from **users**, **browsers**, or **external systems**, and should be treated as **untrusted** unless validated.
    
    ### Common JavaScript Sources
    
    ```jsx
    prompt("Enter value");          // User input
    location.href                  // URL
    document.cookie                // Cookies
    localStorage.getItem("key")     // Browser storage
    ```
    
    ### Key Point
    
    > A source is the starting point of data.
    > 
    
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
    
    ### Key Point
    
    > A sink is the endpoint where data is consumed.
    > 
    
    ---
    
    ## Data Flow Example
    
    ```jsx
    let userInput = prompt("Enter your name"); // Source
    document.getElementById("output").innerHTML = userInput; // Sink
    
    ```
    
    - `prompt()` → Source
    - `innerHTML` → Sink
    
    ---
    

1. Visit the webpage

![image.png](Lab%20DOM%20XSS%20in%20document%20write%20sink%20using%20source%20lo/image.png)

1. Use the XSS payload to exploit the vulneriblity

![image.png](Lab%20DOM%20XSS%20in%20document%20write%20sink%20using%20source%20lo/image%201.png)

![image.png](Lab%20DOM%20XSS%20in%20document%20write%20sink%20using%20source%20lo/image%202.png)

![image.png](Lab%20DOM%20XSS%20in%20document%20write%20sink%20using%20source%20lo/image%203.png)