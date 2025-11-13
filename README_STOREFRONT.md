## Local Development Setup

Follow these steps to run the Storefront project (`source/` monorepo) locally.

### 1. Clone the repository

```bash
git clone https://github.com/davidshulman/storefront.git
cd storefront
```

### 2. Initialize submodules

```bash
git submodule update --init --recursive
```

> The project depends on the `source`, `demo`, `docs`, and `website` submodules. Make sure they finish cloning before continuing.

### 3. Install prerequisites

- Node.js 18 or later
- npm 9+ (or the version bundled with your Node installation)
- Firebase CLI (`npm install -g firebase-tools`)
- Stripe CLI (optional, for webhook testing)

Verify your versions:

```bash
node -v
npm -v
firebase --version
```

### 4. Install dependencies

```bash
cd source
npm install
cd functions
npm install
cd ..
```

### 5. Configure environment files

Create the following JSON files if they are missing:

- `source/src/config/firebase.config.json`
- `source/src/config/stripe.config.json`
- `source/functions/src/config/stripe.config.json`
- `source/.firebaserc`

Example emulator-friendly values:

```json
// source/src/config/firebase.config.json
{
  "firebase": {
    "apiKey": "your-api-key",
    "authDomain": "your-project.firebaseapp.com",
    "projectId": "your-project",
    "storageBucket": "your-project.appspot.com",
    "messagingSenderId": "1234567890",
    "appId": "1:1234567890:web:abcdef123456"
  },
  "emulators": {
    "enabled": true,
    "auth": { "host": "localhost", "port": 9099 },
    "firestore": { "host": "localhost", "port": 8080 },
    "functions": { "host": "localhost", "port": 5001 }
  }
}
```

```json
// source/src/config/stripe.config.json
{
  "stripe": {
    "public_api_key": "pk_test_your_publishable_key",
    "plans": []
  }
}
```

```json
// source/functions/src/config/stripe.config.json
{
  "stripe": {
    "secret_api_key": "sk_test_your_secret_key",
    "publishable_api_key": "pk_test_your_publishable_key",
    "end_point_secret": "whsec_your_webhook_secret",
    "plans": []
  }
}
```

```json
// source/.firebaserc
{
  "projects": {
    "default": "your-project"
  }
}
```

Replace the placeholder values with credentials from your Firebase project and Stripe account. Keep these files out of version control if they contain secrets.

### 6. Build the project

```bash
cd source
npm run build
cd functions
npm run build
cd ..
```

### 7. Start development servers

Open two terminals:

**Terminal A – Vite dev server**

```bash
cd source
npm run dev -- --host
```

Visit http://127.0.0.1:5173 to view the live-reloading app.

**Terminal B – Firebase emulators**

```bash
cd source
firebase emulators:start
```

This launches Hosting (http://localhost:5002), Functions (http://localhost:5001), Auth (http://localhost:9099), Firestore (http://localhost:8080), and the Emulator UI (http://localhost:4000).

### 8. (Optional) Stripe webhooks

If you need to exercise billing flows, run the Stripe CLI in another terminal:

```bash
stripe listen --forward-to http://127.0.0.1:5001/<your-project>/us-central1/stripeWebhook
```

Update `source/functions/src/config/stripe.config.json` with the webhook secret shown by the CLI, then rebuild the functions (`npm run build` inside `source/functions`).

---

With these steps complete, you can explore the Storefront UI locally and iterate on features against the Firebase emulator suite.

