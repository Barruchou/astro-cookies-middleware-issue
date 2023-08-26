If you use an `Astro.cookies.set(...)` and a middleware that rewrites the response (as in your [documentation](https://docs.astro.build/fr/guides/middleware/#example-redacting-sensitive-information)), the cookies are not forwarded.

```javascript
// src/middleware.js

export const onRequest = async (context, next) => {
    const response = await next();
    const html = await response.text();

    return new Response(html, {
        status: 200,
        headers: response.headers
    });
};
```

This does not work :
```javascript
// src/pages/foo.astro

Astro.cookies.set("astro", "cookie", {
    httpOnly: true,
    path: "/",
    sameSite: "strict",
    maxAge: 1704085200,
  });
```

But this works :
```javascript
// src/pages/foo.astro

Astro.response.headers.set(
    "Set-Cookie",
    "common=cookie; Path=/; HttpOnly; SameSite=Strict; Max-Age=1704085200",
  );
```