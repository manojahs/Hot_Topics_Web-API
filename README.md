# Hot_Topics_Web-API
----------------------------

| Feature                | Stateless Protocol         | Stateful Protocol             |
| ---------------------- | -------------------------- | ----------------------------- |
| **Session memory**     | None (each request is new) | Maintains session info        |
| **Request dependency** | Independent requests       | Requests depend on previous   |
| **Server complexity**  | Low                        | High (tracks session/state)   |
| **Performance**        | Typically faster           | Can be slower (more overhead) |
| **Scalability**        | High                       | Lower (more memory required)  |
| **Failure handling**   | Easier (reconnect & retry) | Harder (state might be lost)  |


| Feature                  | JWT (Token-Based)                                                 | Session (Cookie-Based)                                   |
| ------------------------ | ----------------------------------------------------------------- | -------------------------------------------------------- |
| **Storage (Server)**     | Stateless: No data stored on server                               | Stateful: Session data stored on server                  |
| **Storage (Client)**     | Token stored in browser (localStorage, sessionStorage, or cookie) | Session ID stored in cookie                              |
| **Scalability**          | Highly scalable                                                   | Less scalable (requires sticky sessions or shared cache) |
| **Token Content**        | Self-contained (includes user data)                               | Only a reference (session ID)                            |
| **Authentication Check** | Verifies JWT signature                                            | Looks up session data in server store                    |
| **Revocation**           | Harder (requires blacklist)                                       | Easy (delete session from server)                        |
| **Used in**              | APIs, SPAs, mobile apps                                           | Traditional websites                                     |


https://chatgpt.com/c/681a3186-59b0-8013-ac96-6265eeed7028
