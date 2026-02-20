# Firebase setup – connect login to your database

Follow these steps so the **login page** uses Firebase and you can **see your database** in the Firebase Console.

## 1. Create a Firebase project

1. Go to **[Firebase Console](https://console.firebase.google.com/)**.
2. Click **Add project** (or use an existing project).
3. Name it (e.g. "Patient Record System") and finish the wizard.

## 2. Enable Authentication (Email/Password)

1. In the left sidebar, open **Build → Authentication**.
2. Click **Get started**.
3. Open the **Sign-in method** tab.
4. Click **Email/Password**, turn **Enable** ON, then **Save**.

## 3. Create Firestore Database

1. In the left sidebar, open **Build → Firestore Database**.
2. Click **Create database**.
3. Choose **Start in test mode** (for development), pick a region, then **Enable**.
4. (Optional) Later you can add [security rules](https://firebase.google.com/docs/firestore/security/get-started) to restrict who can read/write.

## 4. Add your web app and get config

1. Click the **gear icon** next to "Project Overview" → **Project settings**.
2. Under **Your apps**, click the **Web** icon `</>`.
3. Register the app (e.g. name "Patient Record Web").
4. Copy the **firebaseConfig** object (apiKey, authDomain, projectId, etc.).

## 5. Put the config in your project (use .env)

1. Copy **`.env.example`** to **`.env`** in the project root.
2. Fill in the values from the Firebase Console (same names as in the example).
3. Run **`npm run config`** to generate **`js/firebase-config.js`** from `.env`.  
   Or edit **`js/firebase-config.js`** directly and paste your `firebaseConfig` values.  
   **Do not commit `.env`** (it is in `.gitignore`); use `.env.example` as a template for others.

## 6. Add your site to Authorized domains (fix “unauthorized-domain” error)

If you see **"This domain is not authorized for OAuth operations"** or **auth/unauthorized-domain**:

1. **[Open Firebase Authentication settings](https://console.firebase.google.com/project/joseph-project-c03af/authentication/settings)** (opens your project).
2. Find **Authorized domains** on the page.
3. Click **Add domain** and add **`localhost`** (for local testing) or **`127.0.0.1`** if you use that, or your live domain later.
4. Save. Then try signing in again.

The login page will show your current domain and an **Open Firebase Console** button when this setup is needed.

## 7. Enable Google, Facebook, and Apple sign-in

If you see **"The given sign-in provider is disabled"** or **auth/operation-not-allowed** when using Facebook or Apple (or Google), enable that provider in Firebase:

**[Open Sign-in method in Firebase](https://console.firebase.google.com/project/joseph-project-c03af/authentication/providers)** → click the provider (e.g. **Facebook** or **Apple**) → turn **Enable** ON → Save.

### Enable Facebook sign-in

1. Open **[Firebase → Sign-in method](https://console.firebase.google.com/project/joseph-project-c03af/authentication/providers)** and click **Facebook**.
2. Turn **Enable** ON.
3. Create a Facebook app (if you don’t have one):
   - Go to [Facebook for Developers](https://developers.facebook.com/) → **My Apps** → **Create App** → **Consumer**.
   - In the app dashboard: **Settings → Basic** → copy **App ID** and **App Secret**.
4. In Firebase Facebook settings, paste **App ID** and **App Secret**.
5. In Facebook Developer dashboard, add the **OAuth redirect URI** that Firebase shows (e.g. `https://joseph-project-c03af.firebaseapp.com/__/auth/handler`).
6. Click **Save** in Firebase.

### Enable Apple sign-in

1. Open **[Firebase → Sign-in method](https://console.firebase.google.com/project/joseph-project-c03af/authentication/providers)** and click **Apple**.
2. Turn **Enable** ON.
3. You need an [Apple Developer account](https://developer.apple.com/). In Firebase, follow the prompts to add your **Services ID**, **Apple Team ID**, **Key ID**, and **Private Key** (from Apple Developer → Certificates, Identifiers & Keys). See [Firebase Apple sign-in docs](https://firebase.google.com/docs/auth/web/apple).
4. Click **Save** in Firebase.

### Enable Google sign-in

1. In the same **Sign-in method** page, click **Google**.
2. Turn **Enable** ON, set a **Support email**, then **Save**.

After a provider is enabled, the matching button on your login page will work. The login page will show a short setup guide and an **Open Firebase Console** link if the provider is still disabled.

## 8. Create a user (for testing)

1. In Firebase Console → **Authentication** → **Users**.
2. Click **Add user**, enter an email and password, then **Add user**.
3. (Optional) In **Authentication → Templates**, customize the **Email address verification** template so the "Verify" link works from your app URL.

## 9. See your database

- **Authentication**: Firebase Console → **Authentication** → **Users** (see who signed up / signed in).
- **Firestore**: Firebase Console → **Firestore Database** (see the `loginLog` collection after someone logs in).

From the **Admin Dashboard** (after you log in), use the **"Open Firebase Console →"** button to open your project’s console directly.

## Two-step login flow

1. **Step 1**: User enters email and password → sign-in with Firebase Auth.
2. **Step 2**: If the email is not verified, a verification email is sent and the user sees “Verify your email”. After they click the link in the email, the next sign-in goes straight to the **Admin Dashboard**.
3. **Redirect**: Once signed in (and verified if required), the user is redirected to **admin-dashboard.html**.

## Firestore: `loginLog` collection

Each successful login is written to a Firestore collection **`loginLog`** with:

- `email` – user email  
- `uid` – Firebase user ID  
- `timestamp` – server timestamp  

You can view and query this in **Firestore Database** in the Firebase Console.
