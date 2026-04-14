<div align="center">

# 🏠 Shared Space

**The gorgeous, real-time household management app for people who live together.**

[![Flutter](https://img.shields.io/badge/Flutter-3.41.6-blue?logo=flutter)](https://flutter.dev)
[![Dart](https://img.shields.io/badge/Dart-3.11.4-blue?logo=dart)](https://dart.dev)
[![Firebase](https://img.shields.io/badge/Firebase-chaty--86c83-orange?logo=firebase)](https://firebase.google.com)
[![Platform](https://img.shields.io/badge/Platform-Android%207.0%2B-green?logo=android)](https://developer.android.com)
[![License](https://img.shields.io/badge/License-MIT-lightgrey)](LICENSE)

*Eliminate the friction of shared living — beautifully.*

</div>

---

## ✨ What is Shared Space?

Shared Space is a **Flutter-first, Firebase-powered Android app** that makes shared living transparent, fair, and stress-free. It is the mobile companion to an existing web platform, sharing the same live Firestore database.

### Why it exists

People living together constantly battle:
- 💸 *"Who owes whom for the electricity bill?"*
- 🧹 *"Whose turn is it to clean the kitchen?"*
- 🚿 *"Is the shower free? How long is the wait?"*
- 📦 *"Did anyone buy milk yet?"*

Shared Space solves all of this with **real-time sync**, **smart automations**, and a **stunning design** that people actually enjoy using.

---

## 🚀 Features

| Module | Status | Description |
|---|---|---|
| 🔐 **Authentication** | 🟡 In Progress | Google Sign-In + Email/Password via Firebase Auth |
| 🏠 **Onboarding** | 🟡 In Progress | Create or join an apartment with invite codes |
| 📊 **Dashboard** | 📋 Planned | Real-time household overview with glanceable stats |
| 💰 **Expense Tracking** | 📋 Planned | Split bills, track debts, settle up |
| ✅ **Chore Management** | 📋 Planned | Assign tasks, set due dates, mark done |
| 🗑️ **Trash Rotation** | 📋 Planned | Auto-advancing fair rotation with audit log |
| 🚿 **Shower Queue** | 📋 Planned | Real-time booking with hot-water buffer |
| 🛒 **Grocery List** | 📋 Planned | Collaborative shopping list with real-time sync |
| 📅 **Shared Calendar** | 📋 Planned | Household events with reminders |
| 🔔 **Push Notifications** | 📋 Planned | Smart, contextual FCM notifications |
| 🌐 **RTL Support** | 📋 Planned | Full Arabic language + mirror layouts |

> **Legend**: ✅ Complete · 🟡 In Progress · 📋 Planned · 🚧 Blocked

---

## 🛠 Tech Stack

### Frontend
- **[Flutter 3.41+](https://flutter.dev)** (stable) / **Dart 3.11+**
- **[Riverpod 2.5+](https://riverpod.dev)** — state management & DI
- **[go_router](https://pub.dev/packages/go_router)** — declarative routing with deep links
- **[freezed](https://pub.dev/packages/freezed)** + **[json_serializable](https://pub.dev/packages/json_serializable)** — immutable models
- **[easy_localization](https://pub.dev/packages/easy_localization)** — EN/AR i18n with RTL
- **[flutter_animate](https://pub.dev/packages/flutter_animate)** — micro-interactions & transitions
- **[Hive](https://pub.dev/packages/hive_flutter)** — offline cache & pending-write queue

### Backend (existing, shared with web version)
- **[Firebase Auth](https://firebase.google.com/products/auth)** — Google + Email/Password
- **[Cloud Firestore](https://firebase.google.com/products/firestore)** — real-time database with offline persistence
- **[Firebase Cloud Messaging](https://firebase.google.com/products/cloud-messaging)** — push notifications
- **[Firebase Crashlytics](https://firebase.google.com/products/crashlytics)** *(optional)*

### Design
- **Fonts**: Outfit (Latin) · Tajawal (Arabic) via Google Fonts
- **Color palette**: Blue `#2563EB` primary · Indigo `#6366F1` secondary
- **8-point spacing system**, consistent border radii, gradient cards
- **Liquid Glass bottom navigation bar**

---

## 📱 Design System Highlights

```
Colors      → AppColors       (primary, semantic, background, text)
Typography  → AppTextStyles   (pageTitleLarge → caption, responsive to locale)
Spacing     → AppSpacing      (xs=4, sm=8, md=16, lg=24, xl=32, xxl=48)
Radii       → AppBorderRadius (small=8 → xxLarge=40)
Shadows     → AppShadows      (card, primaryButton)
Gradients   → AppGradients    (primaryCard, secondaryCard)
```

All animations follow the spec:
- **Page transitions**: fade + slide, 300 ms ease-out
- **Bottom sheet open**: slide-up, 350 ms cubic-bezier
- **Button press**: scale 0.95 over 100 ms
- **Success**: checkmark draw (500 ms) + confetti burst

---

## 🗄 Database Architecture

The app connects to an **existing Firestore database** (`chaty-86c83`) shared with the production web application. The schema uses a **flat-collection pattern** for performance and rule simplicity:

```
/users/{userId}
/apartments/{apartmentId}
/apartmentMembers/{apartmentId_userId}
/expenses/{expenseId}
/expenseSplits/{splitId}
/chores/{choreId}
/groceries/{groceryId}
/calendarEvents/{eventId}
/announcements/{announcementId}
/showerSlots/{slotId}
/trashHistory/{historyId}
/inviteCodes/{code}
```

Security rules enforce apartment-scoped access via `isApartmentMember(apartmentId)` checks using the composite `{apartmentId}_{userId}` member document pattern. See [`firestore.rules`](firestore.rules) for the full rule set.

---

## 📂 Project Structure

```
lib/
├── core/
│   ├── theme/          # AppColors, AppTextStyles, AppSpacing, AppGradients
│   ├── routing/        # go_router configuration & guards
│   ├── firebase/       # Firebase initialization helpers
│   └── providers/      # Global Riverpod providers (auth, apartment, connectivity)
├── features/
│   ├── auth/           # Login, onboarding, apartment creation/joining
│   ├── dashboard/      # Home screen with stats, shower widget, next-up
│   ├── expenses/       # Expense list, add flow, balances, settlement
│   ├── chores/         # Chore list, add/edit, completion
│   ├── trash/          # Rotation display, mark done, history
│   ├── shower/         # Queue overview, booking flow, countdown
│   ├── groceries/      # Shopping list, add item, purchase flow
│   ├── calendar/       # Monthly grid, event list, add event
│   └── settings/       # Profile, apartment settings, member management
├── models/             # freezed data models (Expense, Chore, ShowerSlot …)
├── services/           # Firestore repositories (ExpenseService, ChoreService …)
└── l10n/               # easy_localization string keys
assets/
├── translations/
│   ├── en.json
│   └── ar.json
└── images/             # Illustrations for empty states
```

---

## 🏁 Getting Started

### Prerequisites

- Flutter 3.41+ (`flutter upgrade`)
- Android SDK 24+ (Android 7.0)
- A Google account with access to the `chaty-86c83` Firebase project
- `firebase_options.dart` generated via `flutterfire configure`

### Installation

```bash
# 1. Clone the repository
git clone https://github.com/HasanWael/shared-space-flutter.git
cd shared-space-flutter

# 2. Install dependencies
flutter pub get

# 3. Generate code (freezed, json_serializable, riverpod)
dart run build_runner build --delete-conflicting-outputs

# 4. Run on Android
flutter run
```

### Environment

Firebase configuration is generated by `flutterfire configure` and committed as `lib/firebase_options.dart`. Never commit raw API keys. Use Firebase App Check for production hardening.

---

## 🗺 Roadmap

### Phase 1 — Foundation *(current)*
- [x] Project spec & constitution
- [ ] Flutter scaffold with design system
- [ ] Firebase Auth (Google + Email/Password)
- [ ] Apartment creation & joining
- [ ] Bottom navigation shell

### Phase 2 — Core Features
- [ ] Dashboard with real-time stats
- [ ] Expense tracking & balance calculation
- [ ] Chore management
- [ ] Trash rotation

### Phase 3 — Resource Management
- [ ] Shower queue with hot-water buffer
- [ ] Grocery list
- [ ] Shared calendar
- [ ] Member management & settings

### Phase 4 — Polish & Notifications
- [ ] FCM push notifications + deep linking
- [ ] Full animation pass
- [ ] Offline mode & sync queue
- [ ] Arabic RTL validation

### Phase 5 — Launch
- [ ] Beta release (Firebase App Distribution)
- [ ] Bug-fix iteration
- [ ] Google Play production release

---

## 🤝 Contributing

This project is developed using the **Spec Kit** agent-assisted workflow:

1. Features begin with `speckit-specify` → `speckit-plan` → `speckit-tasks`
2. Implementation via `speckit-implement`
3. Every PR updates this README and closes the linked GitHub issue

All contributions MUST comply with the [project constitution](.specify/memory/constitution.md).

---

## 📄 License

MIT — see [LICENSE](LICENSE) for details.

---

<div align="center">

Built with ❤️ using Flutter & Firebase

</div>
