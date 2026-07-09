# Global Market 💱

A complete, commercial-grade **Expo + React Native (Expo Router)** app that runs on **Web, Android, and iOS** from a single codebase.

Built with: **Expo Router**, **Firebase Auth / Firestore / Storage**, **Expo Notifications**,
**Expo Image Picker**, **Stripe** + **Paystack**, **Ionicons**, and a Tailwind-inspired
blue & gold theme with light/dark support.

---

## 🚀 Run it

```bash
# 1. Install dependencies
npm install
# or
npx expo install

# 2. Configure environment
cp .env.example .env
#   -> fill in your Firebase + Google + Stripe/Paystack keys

# 3. Start
npx expo start          # all platforms
npx expo start --web    # web
npx expo start --ios    # iOS simulator
npx expo start --android
```

The app runs even **without** keys (you'll just see a placeholder login and the UI);
wire up Firebase to make auth, data, and storage live.

---

## 🔧 Firebase setup

1. Create a Firebase project → add **Web**, **iOS**, and **Android** apps.
2. Paste the config into `.env` (`EXPO_PUBLIC_FIREBASE_*`).
3. Enable **Authentication** → Email/Password **and** Google.
4. Create a **Cloud Firestore** database (start in test mode for dev).
5. Enable **Storage** (for product/avatar images).
6. For Web Push, generate a **VAPID key** and set `EXPO_PUBLIC_FIREBASE_VAPID_KEY`.

### Firestore collections used
`users`, `signals`, `tips`, `courses`, `products`, `carts/{uid}/items`, `orders`,
`chats`, `chats/{id}/messages`, `notifications`.

### Security rules (starter)
Lock reads to authenticated users and writes to owners/admins. See the
Firebase console → Firestore → Rules. (A sample ruleset is recommended before prod.)

---

## 🟦 Google sign-in

`expo-auth-session` is used for cross-platform Google login (web + native).
Add your **OAuth client IDs** to `.env`:
- `EXPO_PUBLIC_GOOGLE_WEB_CLIENT_ID`
- `EXPO_PUBLIC_GOOGLE_IOS_CLIENT_ID`
- `EXPO_PUBLIC_GOOGLE_ANDROID_CLIENT_ID`

In Google Cloud Console, add the authorized redirect URIs:
- Web: your deployed origin + `/__/auth/handler`
- Dev (Expo proxy): `https://auth.expo.io/@<your-account>/global-market`

---

## 💳 Payments (Stripe + Paystack)

- Stripe uses `@stripe/stripe-react-native` `PaymentSheet`.
- Paystack uses `react-native-paystack-webview`.
- **Test mode by default:** if no keys are set (or no backend `PaymentIntent`),
  payments **mock success** so the flow is fully clickable.
- To go live: set `EXPO_PUBLIC_STRIPE_PUBLISHABLE_KEY` and create a
  `PaymentIntent` from a backend (Firebase Cloud Function) — wire it in
  `lib/payments.ts` (`prepareStripePayment`).
- A successful **Premium** purchase sets `users/{uid}.isPremium = true` (Global Market Premium) and
  `premiumExpiry = now + 30d`. Marketplace checkout creates `orders` docs.

---

## 🔔 Notifications

- `expo-notifications` requests permission and stores the **Expo push token**
  (and FCM token where available) on `users/{uid}.expoPushTokens`.
- A global in-app **notification bell** reads the `notifications` collection.
- When an admin posts a Signal/Tip, a global notification is written and shown
  in-app + as a local notification.
- **Real device push** to all users requires a backend using the stored tokens
  (e.g. Firebase Cloud Messaging / Expo Push API). The client-side pieces are
  all in place.

---

## 🧭 Navigation (Expo Router file-based)

```
app/
  (auth)/        login, register, forgot-password, onboarding
  (tabs)/        Home · Signals · Market · Chat · Profile  (bottom tabs)
  signals/       [id] detail, create
  tips/          index, [id], create
  mentorship/    index, [id] video, create
  marketplace/   product/[id], add, cart, seller
  chat/          [id] room
  profile/       edit, orders, mentor, admin
  payment/       checkout, success
```

Roles (`admin | mentor | seller | user`) gate create screens, the Mentor
Dashboard, and the Admin Dashboard.

---

## 🎨 Theme

`lib/theme.ts` holds the blue + gold tokens; `context/ThemeContext.tsx` provides
light / dark / system switching (persisted). Components are styled with a
Tailwind-like utility approach via `StyleSheet` for guaranteed runnability.
You can swap in **NativeWind** later without changing component logic.

---

## ✅ Feature checklist

- [x] Email/Password + Google auth, register, forgot password, onboarding
- [x] User saved to Firestore `users` with role/isPremium/premiumExpiry
- [x] Home dashboard with subscription status + 3 quick cards
- [x] Forex Signals list (premium blurred for free users) + create (admin/mentor)
- [x] Betting Tips list + create
- [x] Mentorship courses + video player
- [x] Marketplace: search, categories, grid, detail carousel, cart, add product
- [x] Seller dashboard (products, orders, sales stats)
- [x] Payments: Stripe PaymentSheet + Paystack + mock test mode + premium unlock
- [x] Real-time chat (direct + Premium Signals Discussion group) with image upload
- [x] Media upload to Firebase Storage (avatars, products, chat images)
- [x] Notifications: permission, token storage, global push + in-app bell
- [x] Profile: edit, orders, seller toggle, mentor/admin dashboards, logout
- [x] Admin: create signals/tips/courses, manage users, approve products, push
- [x] Responsive web UI, empty states, dark/light theme
- [x] Firestore real-time listeners throughout

---

## 📦 Scripts

```bash
npm start        # expo start
npm run web      # expo start --web
npm run ios      # expo start --ios
npm run android  # expo start --android
```
