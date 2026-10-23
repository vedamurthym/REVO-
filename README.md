# 🔴 REVO — College Store App

**A token-based mobile ordering system that turns a chaotic campus store into a smooth, app-driven experience.**

Order from your phone. Track it live. Show up only when it's ready.

---

## 📌 Overview

REVO is a mobile ordering platform built for college campuses, think Swiggy or Zomato, but locked inside your campus. Students browse the store menu, place orders, pay through an in-app wallet, and follow their order in real time. Shopkeepers get a live dashboard to manage orders, menu items, and revenue.

REVO started from a problem we saw ourselves at the college store at **JNN College of Engineering (JNNCE)**, and from the very beginning it was built to work in the real world: real-time order tracking, atomic order placement, automated refunds, and push notifications.

## ❗ The Problem

College canteens and campus stores are broken in very specific ways:

- **Long queues at peak hours:** Students lose 20-30 minutes standing in line between lectures.
- **No order tracking:** After paying, nobody knows when the order will be ready, so everyone hovers around the counter.
- **Cash dependency:** Most stores accept only cash, which makes transactions slow and error-prone.
- **No pickup system:** Ready orders mean shouting, confusion, mix-ups, and cold food.
- **Zero admin visibility:** Shopkeepers can't see pending orders, manage inventory, or track revenue in real time.
- **Refund chaos:** If an order can't be fulfilled or isn't collected, there is no clean refund process.

## 💡 Our Solution

REVO connects three roles in one platform:

| Role | What they can do |
|---|---|
| 🎓 **Student** | Browse the menu, add to cart, place an order, pay via wallet, track it live, and collect using a token |
| 🧑‍🍳 **Shopkeeper / Admin** | See live incoming orders, mark items ready, manage menu items, and view dashboard stats |
| ⚙️ **System** | Auto-expires uncollected orders, triggers refunds, cleans stuck orders, and sends push notifications |

## ✨ Key Features

- **Live order tracking:** Status moves `pending → preparing → ready → collected` and updates on screen without refreshing.
- **Token-based pickup:** Every order gets a token number (for example, Token #42), so there is no shouting and no mix-ups.
- **In-app wallet payments:** Balance is deducted instantly when the order is placed.
- **Admin dashboard:** Live orders, menu management, availability toggles, price updates, revenue, and order counts.
- **Automatic refunds:** Orders not collected within the time window expire and the money returns to the student's wallet.
- **Push notifications:** Critical events notify the right user through Firebase Cloud Messaging.
- **Secure by design:** Row Level Security on all tables, so students only see their own data.

## 🛠️ Tech Stack

| Layer | Technology |
|---|---|
| Frontend (Mobile) | Flutter (Dart), cross-platform for iOS and Android |
| Backend / Database | Supabase (PostgreSQL 15+) |
| Authentication | Supabase Auth (Email + Password) |
| Realtime | Supabase Realtime for live order status updates |
| Push Notifications | Firebase Cloud Messaging (FCM) via Supabase Edge Functions |
| State Management | Flutter Provider |
| Payment | Dummy wallet (Razorpay-ready for production) |
| Scheduled Jobs | pg_cron (runs inside PostgreSQL) |
| Security | Row Level Security (RLS) on all tables |

## 🔄 How It Works

### Student Side

1. Sign up or log in and land on the home screen with store categories and items.
2. Browse the menu, add items to the cart, and review the total.
3. Place the order. The wallet balance is deducted instantly and the order enters the `pending` state.
4. Receive a token number (for example, Token #42) and a confirmation push notification.
5. Watch the live status: `pending → preparing → ready`.
6. When the token is called, show up, collect the order, and the status moves to `collected`.

### Shopkeeper Side

1. Log in as admin and see a live dashboard of all incoming orders.
2. Accept an order, start preparing, mark it ready, and the token is announced.
3. Manage the menu: add, edit, or remove items, toggle availability, and update prices.
4. Monitor revenue and order counts in real time.

### System (Automated)

- Every 5 minutes, `pg_cron` cleans orders stuck in `preparing`, which prevents inventory lock.
- If a `ready` order isn't collected within the time window, it auto-expires and a refund goes back to the student's wallet.
- All critical events trigger push notifications to the relevant user.

## 🧠 Under the Hood

- **Atomic order placement:** The entire order logic lives in a PostgreSQL stored function, `place_order`. It validates stock, deducts the wallet, creates the order record, and assigns a token in a single transaction. There are no partial states.
- **Row Level Security:** Students can only see their own orders, while admins can see everything.
- **Realtime updates:** Supabase Realtime channels stream database changes straight to the Flutter app, so the order status card updates without polling.
- **Server-side notifications:** Edge Functions (Deno/TypeScript on Supabase) handle FCM token registration and push dispatch, keeping notification logic secure.
- **Refund state machine:** A separate set of PostgreSQL functions moves a refund through `pending → processing → completed`, with guards against double refunds.
- **Exact wallet math:** The wallet is stored as an integer balance in paise, the smallest currency unit, which avoids floating-point errors.

## 📊 Project Status

REVO was built as a **real-world system from day one**, not a classroom demo, for a campus like JNN College of Engineering. It solves a real problem we observed at our own college store, and it works end to end: students order, pay, and track live, while shopkeepers manage everything from their own dashboard.

It is built on production-grade foundations: atomic transactions, Row Level Security on every table, server-side push notifications, automated refunds, and scheduled cleanup jobs. The only piece still in placeholder form is the payment layer, which uses a **dummy wallet** and is **Razorpay-ready**, so going live with real money is a matter of plugging in the gateway.

While presenting this to the department, we learned that a similar campus ordering initiative had already been started by the AIML department head, in discussion with the society department. We see this as a good sign: the problem is real and worth solving. REVO stands as our independent, end-to-end implementation, and the ideas, architecture, and lessons from it can feed into any collaborative effort.

## 🚀 Future Scope

- Integrate **Razorpay** for real wallet top-ups and payments.
- Add **order history and spending insights** for students.
- Support **multiple stores** (canteen, stationery, and more) on one campus.
- Add **pre-scheduled orders** so students can order before a lecture ends.
- Provide **analytics for shopkeepers**, such as peak hours and best-selling items.
- Offer **ratings and feedback** on items and stores.

## 📚 What We Learned

- Designing a multi-role application with secure access control.
- Putting business logic inside the database for atomic, consistent transactions.
- Building real-time experiences with Supabase Realtime.
- Writing server-side push notification logic with Edge Functions.
- Scheduling background jobs with `pg_cron`.
- Designing a safe refund flow with state-machine guards.
- Building as a team: splitting the work, sharing a codebase, and shipping together.
- Validating an idea against real-world needs, and handling it when others are already working on the same problem.

## ⚙️ Getting Started

> Update the paths and values below to match your repository.

```bash
# Clone the repository
git clone https://github.com/vedamurthym/REVO-.git
cd REVO-

# Install Flutter dependencies
flutter pub get

# Run the app
flutter run
```

You will also need to:

1. Create a Supabase project and run the SQL for the tables, functions (`place_order`, refund functions), RLS policies, and `pg_cron` jobs.
2. Add your Supabase URL and anon key to the app configuration.
3. Set up Firebase (FCM) and deploy the Edge Functions for push notifications.

## 🔐 Security Considerations

- Never commit Supabase service keys, Firebase credentials, or API secrets.
- Only use the public anon key inside the mobile app.
- Keep RLS enabled on every table.
- Review configuration files before pushing to a public repository.

## 👨‍💻 Author

**Vedamurthy M** and **[Friend's Name]**

Engineering students at JNN College of Engineering, interested in AI, Machine Learning, and building real-world applications.

> Replace `[Friend's Name]` with your friend's name and add GitHub or LinkedIn links for both of you.

## 📄 License

This project is available for learning and educational purposes. Add a license file if you want to define formal permissions for reuse and distribution.

---

**In one line:** REVO turns a chaotic college canteen into a clean, token-based ordering system. Order from your phone, track it live, and just show up when it's ready. 🔴
