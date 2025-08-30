## Sentry

This project uses Sentry to notify about unexpected errors.

The website folder will have a src/lib/errors.ts file with an exported function `notifyError(error: Error, contextMessage: string)`.

You should ALWAYS use notifyError in these cases:

- create a new Spiceflow api app, put notifyError in the onError callback with context message including the api route path
- suppressing an error for operations that can fail. instead of doing console.error(error) you should instead call notifyError
- wrapping a promise with Cloudflare `waitUntil`. Add a .catch and a notifyError so errors are tracked

This function will add the error in Sentry so that the developer is able to track users errors

## errors.ts file

If a package is missing the errors.ts file here is the template for adding one.

Notice that

- dsn should be replaced by the user with the right one. Ask to do so
- use the sentries npm package, this handles correctly every environment like Bun, Node, Browser, etc

```tsx
import { captureException, flush, init } from "sentries";

init({
  dsn: "https://e702f9c3dff49fd1aa16500c6056d0f7@o4509638447005696.ingest.de.sentry.io/4509638454476880",
  integrations: [],
  tracesSampleRate: 0.01,
  profilesSampleRate: 0.01,
  beforeSend(event) {
    if (process.env.NODE_ENV === "development") {
      return null;
    }
    if (process.env.BYTECODE_RUN) {
      return null;
    }
    if (event?.["name"] === "AbortError") {
      return null;
    }

    return event;
  },
});

export async function notifyError(error: any, msg?: string) {
  console.error(msg, error);
  captureException(error, { extra: { msg } });
  await flush(1000);
}

export class AppError extends Error {
  constructor(message: string) {
    super(message);
    this.name = "AppError";
  }
}
```

## AppError

Every time you throw an user readable error you should use AppError instead of Error

AppError messages will be forwarded to the user as is, normal Error instances instead could have their messages obfuscated
