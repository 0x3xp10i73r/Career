# Blind OS command injection with time delays

1. Use Burp Suite to intercept and modify the request that submits feedback.
2. Modify the `email` parameter, changing it to:`email=x||ping+-c+10+127.0.0.1||`
3. Observe that the response takes 10 seconds to return.

- x → harmless command that always fails (exit code ≠ 0)
- || → “run the next command only if the previous one failed”
- `ping -c 10 127.0.0.1` → takes ~10 seconds to finish
- final || → ignored (nothing after it)

![image.png](Blind%20OS%20command%20injection%20with%20time%20delays/image.png)

![image.png](Blind%20OS%20command%20injection%20with%20time%20delays/image%201.png)