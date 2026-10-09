# Render-FREE-Keep-Alive

A simple Cron-based script to periodically send automated requests to your application's health endpoint when hosting on Render's Free Tier.

## Why?

Render's Free Tier spins down web services after 15 minutes without inbound requests. This script uses Cron to send a request every 14 minutes to your configured health-check URL, with the intention of keeping your application active.

## Setup Instructions

### 1. Install the dependency

Run the following command in your server/backend directory:

```bash
npm install cron
```

### 2. Add the Cron script

Copy the `cron.js` file into your server/backend directory.

Replace:

```js
/*<your client/frontend URL>*/
```

with your frontend/client URL environment variable, as required by your script.

### 3. Import the job

In your main server file (`server.js` or `index.js`), import the job:

```js
import job from "./cron.js";
```

### 4. Start the job

Under your `app.listen()` function, add:

```js
job.start();
```

Your Cron job will now start when your server starts.

## Run Only in Production (Recommended)

If you want the Cron job to run only when your application is deployed in production on Render, use the following instead:

```js
process.env.NODE_ENV === "production" && job.start();
```

Place this below your `app.listen()` function.

### Configure `NODE_ENV`

**Locally**

In your `.env` file, set `NODE_ENV` to `development` or another value other than `production`:

```env
NODE_ENV=development
```

**On Render**

1. Open your Render dashboard.
2. Select your web service.
3. Navigate to **Environment**.
4. Add an environment variable:

   - **Key:** `NODE_ENV`
   - **Value:** `production`

5. Save your changes and redeploy if required.

The Cron job will start only when `NODE_ENV` is set to `production`.

**Example**

```javascript
import job from "./cron.js";

app.listen(process.env.PORT, () => {
    process.env.NODE_ENV === "production" && job.start();
});
```

## Important Notes

- Ensure your configured URL points to a valid, reachable endpoint.
- Ensure you have configured "/health" a valid, reachable endpoint.
- This is a workaround, not a guarantee of uninterrupted availability. Render may still spin down or restart free services, and you should review Render's current policies before relying on periodic pings.
