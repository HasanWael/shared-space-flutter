# Product Requirements Document (PRD)

**Project Name:** Shared Space Management Mobile App  
**Document Version:** 2.0 (Flutter Mobile - Android)  
**Target Platform:** Android (API 24 / Android 7.0+)  
**Last Updated:** April 14, 2026

---

## 1. Executive Summary

The Shared Space Management Mobile App is a **gorgeous, real-time, native Android application** built with Flutter that eliminates the friction of shared living. It provides roommates, families, and co-living communities with an elegant mobile platform to manage shared expenses, assign chores, maintain grocery lists, coordinate schedules, and manage shared resources like shower queues and trash duty rotations.

**Key Differentiators:**
- ✨ **Stunning custom design system** with smooth animations and delightful micro-interactions
- 🔄 **Real-time sync** across all devices with offline-first architecture
- 📲 **Smart push notifications** that keep households running smoothly
- 🌐 **RTL support** for Arabic language with seamless layout mirroring

---

## 2. Problem Statement & Value Proposition

### Problem
People living in shared spaces struggle with:
- **Financial confusion:** "Who owes whom for the electricity bill?"
- **Chore accountability:** "Whose turn is it to take out the trash?"
- **Resource conflicts:** "Is the shower free? How long is the wait?"
- **Communication overhead:** Endless group chats that bury important information
- **Lack of transparency:** No clear history of who did what and when

### Value Proposition
By providing a **beautiful, transparent, real-time mobile dashboard** with:
- Automated rotations and notifications
- Intelligent debt calculation and settlement tracking
- Resource queuing with hot water buffer management
- Offline-first architecture for uninterrupted access

The app **reduces roommate conflicts, ensures fair distribution of costs and labor, and streamlines household communication**.

---

## 3. Target Audience

### Primary Users
- **College students** sharing dorms or off-campus housing (18-24 years)
- **Young professionals** in co-living apartments (24-35 years)
- **Families** wanting to organize household chores and groceries

### User Characteristics
- Heavy mobile users (5+ hours daily screen time)
- Comfortable with cloud-based apps
- Value transparency and fairness in shared living
- Prefer visual, intuitive interfaces over complex settings
- Appreciate beautiful, well-designed applications

---

## 4. Core Features & Functional Requirements

### 4.1. Authentication & Onboarding

#### 4.1.1. Google OAuth Authentication & Email/Password Authentication
- **Secure sign-in** via Firebase Auth with Google Provider
- **Single-tap authentication** using device Google account
- **Automatic profile setup** using Google display name and email
- **Session persistence** - user stays logged in until manual logout

#### 4.1.2. Space Creation
- User can **create a new "Apartment"** space
- Creator automatically becomes **Admin** with full permissions
- Generate unique **6-character alphanumeric invite code** (e.g., `A7K2M9`)
- Generate **shareable invite link** (e.g., `app://join/A7K2M9`)

#### 4.1.3. Space Joining
- Join via **invite code** (manual entry with validation)
- Join via **direct invite link** (deep linking support)
- **Onboarding flow** for first-time apartment members
- Display apartment name and member count before joining

#### 4.1.4. Multi-Space Support
- Users can be **members of multiple apartments** simultaneously
- **Seamless switching** between apartments via dropdown in app bar
- Each apartment maintains **independent data** (expenses, chores, queues)
- Visual indicator showing **currently active apartment**

---

### 4.2. Dashboard (Command Center)

The Dashboard is the **primary landing screen** after login - a personalized, glanceable overview of the household.

#### 4.2.1. Header Section
- **Personalized greeting** based on time of day:
  - Morning (5am-12pm): "Good Morning, [Name] 🌅"
  - Afternoon (12pm-5pm): "Good Afternoon, [Name] ☀️"
  - Evening (5pm-10pm): "Good Evening, [Name] 🌆"
  - Night (10pm-5am): "Good Night, [Name] 🌙"
- **Apartment switcher dropdown** (if member of multiple spaces)
- Current date in readable format (e.g., "Monday, Apr 14")

#### 4.2.2. Quick Stats Widget
Grid of 3-4 **glanceable metric cards**:
- **Pending Bills:** Number of unsettled expenses you're involved in
- **Your Balance:** Net amount you owe or are owed (color-coded: red if owe, green if owed)
- **Active Chores:** Number of incomplete chores assigned to you
- **Grocery Items:** Count of items currently on the shopping list

**Design:** Gradient cards with large numbers and icons

#### 4.2.3. Shower Status Widget
**Real-time shower availability** indicator:
- **If Free:** Green pulsing indicator + "Shower Available" + "Book Now" CTA
- **If Occupied:** 
  - Shows occupant name and profile picture
  - Live countdown timer (MM:SS remaining)
  - Progress bar showing time elapsed
  - Next person in queue (if any)
- **If Queued:** Shows list of upcoming bookings with times
- **Tap to navigate** to full Shower Queue screen

#### 4.2.4. Next Up Section
Highlights the **most immediate pending action** for the user:
- **Trash Turn:** "It's your turn to take out the trash!" (if current in rotation)
- **Overdue Chore:** "Kitchen Cleaning overdue by 2 days" (if has overdue chore)
- **Upcoming Shower:** "Your shower in 15 minutes" (if booked soon)
- **Unsettled Debt:** "You owe Sarah $45" (if has outstanding debt)

**Priority order:** Overdue items → Current turn → Upcoming events → Debts

#### 4.2.5. Recent Activity Feed
Scrollable list of **last 5-7 household events**:
- Expenses added/settled
- Chores completed
- Trash taken out
- Groceries purchased
- Shower bookings made

**Design:** Timeline-style cards with icons, timestamps, and actor names

---

### 4.3. Expense Tracking & Bill Splitting

A comprehensive financial management system for shared costs.

#### 4.3.1. Expense List View
- **Chronological feed** of all expenses (newest first)
- **Filter options:**
  - All expenses
  - Pending (unsettled)
  - Settled
  - Expenses involving me
- **Search functionality** by description or person
- **Sort options:** Date, Amount, Status

**Expense Card Design:**
- **Primary info:** Description + Total amount (large, bold)
- **Metadata:** Paid by [Name] • Split among [X people] • [Date]
- **Your share:** Highlighted amount (e.g., "You owe $15")
- **Status badge:** Pending (orange) / Settled (green)
- **Tap to expand** for full split details

#### 4.3.2. Add Expense Flow
**Multi-step modal/bottom sheet:**

**Step 1: Basic Info**
- Description (text input, required)
- Total amount (number input with currency symbol, required)
- Category dropdown (optional): Groceries, Utilities, Rent, Entertainment, Other
- Date picker (defaults to today)

**Step 2: Who Paid?**
- Single-select list of all apartment members
- Defaults to current user
- Shows member avatars and names

**Step 3: Split Method**
- **Equal Split** (default): Automatically divides among selected members
- **Custom Split:** Manual amount entry per person
- Member selection with checkboxes
- Real-time validation (split must equal total)

**Step 4: Review & Submit**
- Summary card showing all details
- Per-person breakdown visible
- "Add Expense" button
- Cancel option

**Validation:**
- Total amount > 0
- At least one person selected for split
- Custom splits must sum to total amount
- Description not empty

#### 4.3.3. Debt Calculation & Balances
**Automatic settlement optimization** using the following logic:

**Algorithm:**
1. Calculate net balance for each person (total paid - total owed)
2. Simplify transactions to minimize number of payments
3. Display as "Person A owes Person B $X"

**Balances Screen:**
- **Your Net Balance** card at top (large, color-coded)
  - Positive (green): "You are owed $X total"
  - Negative (red): "You owe $X total"
  - Zero (gray): "All settled up! 🎉"
- **Detailed Breakdown:**
  - List of individual debts: "You owe John $30" or "Sarah owes you $15"
  - Each debt has "Settle Up" button
  - Shows related expenses on tap

**Settlement Flow:**
- Tap "Settle Up" on a debt
- Confirmation modal: "Mark $30 payment to John as settled?"
- Payment method note (optional text): "Paid via Venmo", "Cash", etc.
- Updates all affected expenses to "Settled" status
- Sends notification to other party

#### 4.3.4. Expense History
- **Infinite scroll** list of all time expenses
- **Monthly grouping** with subtotals
- **Export option:** Generate CSV or PDF report (future enhancement)
- **Undo deletion:** 10-second grace period after deleting expense

---

### 4.4. Chore Management

Task assignment and tracking system for household responsibilities.

#### 4.4.1. Chore List View
**Default view: My Chores**
- Toggle between: **My Chores** / **All Chores**
- **Status tabs:** Pending / Completed / Overdue
- **Sort by:** Due date, Created date, Assignee

**Chore Card Design:**
- **Title** (bold, medium text)
- **Assignee name** with small avatar
- **Due date** with visual indicator:
  - Green: Due in 3+ days
  - Orange: Due in 1-2 days
  - Red: Overdue (shows "X days overdue")
- **Completion checkbox** (tap to mark done)
- **Swipe actions:** Edit / Delete (admin only)

#### 4.4.2. Add/Edit Chore Flow
**Bottom sheet form:**
- **Chore title** (text input, required)
- **Description** (multi-line text, optional)
- **Assign to** (single-select member picker)
  - Option: "Assign to me"
  - Option: "Unassigned"
- **Due date** (date picker, optional)
  - Quick options: Today, Tomorrow, This Weekend, Next Week
- **Recurring** (future enhancement - skip for MVP)

**Validation:**
- Title not empty
- If due date set, must be today or future

#### 4.4.3. Chore Completion
**Mark as Done:**
- Tap checkbox on chore card
- Shows completion animation (checkmark bounce + confetti)
- Moves to "Completed" tab with timestamp
- Sends notification to apartment (optional setting)
- Updates in real-time for all members

**Undo Completion:**
- 10-second grace period with snackbar "Undo" button
- After grace period, requires admin to reopen

#### 4.4.4. Visual Indicators
- **Overdue chores:** Red background tint + exclamation icon
- **Due today:** Orange accent border
- **Completed:** Strikethrough text + checkmark + faded opacity
- **Unassigned:** Gray dotted border

---

### 4.5. Trash Turn (Rotation System)

Automated, fair rotation system for taking out the trash.

#### 4.5.1. Main Trash Turn Screen
**Current Turn Card (Hero Section):**
- Large, prominent card showing:
  - **If it's your turn:** "Your Turn! 🗑️" with pulsing animation
  - **If it's someone else's:** "[Name]'s Turn" with their avatar
- **"Mark as Done" button** (only visible to current person)
- **Rotation order visualization:** Circular avatar list showing order
- **Last taken:** "[Name] took it out [X time ago]"

**Rotation Order Section:**
- Numbered list of all members in rotation order
- Current person highlighted with accent color
- "Away" members shown as grayed out with "away" badge
- Drag-to-reorder (admin only)

**Action Buttons:**
- **"I Threw It"** - Available to everyone (out-of-turn action)
- **"Mark as Away"** - Toggle for your own status
- **History** - View full audit log

#### 4.5.2. Mark as Done Flow
**When current person taps "Mark as Done":**
1. Confirmation dialog: "Confirm trash taken out?"
2. Logs action with timestamp in Firestore
3. Shifts rotation to next person (skipping "Away" members)
4. Shows success animation
5. Sends notification to next person in rotation
6. Updates dashboard for all members

**Real-time sync:** All members see the update instantly

#### 4.5.3. "I Threw It" Override
**Purpose:** Allow anyone to take out trash without disrupting main rotation

**Flow:**
1. Tap "I Threw It" button
2. Confirmation: "Log out-of-turn trash action? This won't change the rotation."
3. Records action in audit log with "Override" badge
4. Does NOT shift the main rotation
5. Sends thank-you notification to the volunteer

**Use case:** Someone threw it out because the bag was full, not because it was their turn

#### 4.5.4. Away Status Management
**Self-service toggle:**
- User can mark themselves as "Away" (traveling, sick, etc.)
- While away:
  - Rotation automatically skips them
  - Shown as grayed out in rotation list
  - "Away" badge visible
- Toggle back to "Active" when returned
- Admin can force someone to "Away" or "Active"

**Auto-skip logic:**
- When it becomes an "Away" person's turn, system automatically moves to next active person
- Logs skip action in history

#### 4.5.5. Audit Log / History
**Full chronological history screen:**
- **Action type:** Done, Override, Skip (Away)
- **Who:** Name and avatar
- **When:** Relative timestamp (e.g., "2 days ago")
- **Rotation position** at time of action
- Infinite scroll, paginated
- Filter by: All / Done / Overrides / Skips

**Design:** Timeline view with connecting lines between entries

---

### 4.6. Shower Queue (Resource Management)

Real-time booking system for coordinating shower usage with hot water recovery periods.

#### 4.6.1. Shower Queue Overview Screen
**Live Status Hero Card:**
- **If Free:**
  - Green pulsing indicator
  - "Shower Available 🚿"
  - Large "Book Now" button
- **If Occupied:**
  - User's name + avatar
  - Large countdown timer (MM:SS)
  - Circular progress indicator
  - "Ends at [time]"
  - Next person in queue preview
- **If Cooling Down (hot water buffer):**
  - Orange flame icon with timer
  - "Hot water recovering... Ready in [X] mins"
  - Prevents booking during cooldown

**Upcoming Queue:**
- Scrollable list of all scheduled showers
- Grouped by: Now, Today, Tomorrow, Later
- Each booking shows:
  - User name + avatar
  - Start time
  - Duration
  - "Cancel" button (only if your booking)

#### 4.6.2. Book Shower Flow
**Immediate Booking (Shower is Free):**
1. Tap "Book Now"
2. Bottom sheet opens:
   - **Duration picker:** 10 mins, 15 mins, 30 mins, Custom
   - **Start Time:** Defaults to "Now", can schedule for later
   - **Preview:** "Shower occupied until [time], cooldown until [time]"
3. Tap "Confirm Booking"
4. Starts live countdown immediately
5. Sends notification to household

**Future Booking:**
1. Tap "Schedule Later"
2. Date & time picker
3. Duration selection
4. Validation:
   - Cannot overlap with existing bookings
   - Cannot overlap with cooldown periods
   - Must respect hot water buffer
5. Adds to queue
6. Sends reminder notification 5 mins before start

#### 4.6.3. Hot Water Buffer System
**Configuration (Admin Setting):**
- Default: 20 minutes cooldown between showers
- Configurable: 10, 15, 20, 25, 30 minutes
- Enforced automatically by booking system

**How it Works:**
1. User finishes 15-min shower at 8:00 AM
2. Cooldown period: 8:15 AM - 8:35 AM (20 mins)
3. Next booking cannot start before 8:35 AM
4. During cooldown, "Book Now" is disabled
5. Shows countdown: "Hot water ready in 12 mins"

**Smart Validation:**
- When booking, system checks for:
  - Existing bookings in time range
  - Required buffer after previous shower
  - Prevents back-to-back bookings without buffer

#### 4.6.4. Active Shower Management
**For the person currently showering:**
- **Large "I'm Done" button** always visible
- **Extend Time:** +5 mins, +10 mins buttons (if no queue)
- **Real-time countdown** with visual progress ring

**Tap "I'm Done":**
1. Ends shower immediately
2. Starts cooldown timer
3. Notifies next person in queue
4. Logs actual duration vs. booked duration

**Auto-End:**
- When timer hits 0, shower status changes to "Cooldown"
- Sends notification to user: "Your shower time is up!"
- Does NOT force-cancel, but notifies household

**For everyone else:**
- Can view live countdown
- See their position in queue
- Receive notification when it's their turn

#### 4.6.5. Queue Management
**Cancel Booking:**
- Only booking owner can cancel
- Confirmation dialog
- Updates queue in real-time
- Notifies next person if it was an active shower

**Queue Rules:**
- First-come, first-served
- No priority system (MVP)
- Cannot book multiple slots simultaneously
- Maximum 7 days advance booking

---

### 4.7. Shared Groceries

Collaborative shopping list for household items.

#### 4.7.1. Grocery List View
**Active Items (Default Tab):**
- Simple, scannable list
- Each item shows:
  - Item name (medium bold text)
  - Quantity (if specified)
  - Who added it
  - Checkbox to mark as purchased
- Sort by: Recently added, Alphabetical
- Search bar at top

**Purchased Items Tab:**
- History of bought items
- Shows who bought it and when
- Can re-add item to active list

#### 4.7.2. Add Grocery Item
**Quick Add:**
- Floating Action Button (FAB) on list screen
- Bottom sheet with:
  - Item name (text input, required)
  - Quantity (optional): Number + Unit dropdown (pcs, kg, L, etc.)
  - Notes (optional): Brand preference, size, etc.
- Tap "Add to List"
- Real-time sync to all devices

**Batch Add (Future):**
- Add multiple items separated by commas
- Voice input integration

#### 4.7.3. Mark as Purchased
**Single-tap completion:**
- Tap checkbox on item
- Item animates out with slide + fade
- Moves to "Purchased" tab
- Logs who bought it and timestamp
- Optional: Add receipt photo (future)

**Undo:** 10-second grace period with snackbar

#### 4.7.4. Item Management
- **Edit:** Long-press item to edit name/quantity
- **Delete:** Swipe to delete (with confirmation)
- **Re-add:** Tap purchased item to add back to active list
- **Clear All Purchased:** Admin can clear history (with confirmation)

---

### 4.8. Shared Calendar

Household events and scheduling coordination.

#### 4.8.1. Calendar View
**Monthly Grid View:**
- Standard calendar grid showing current month
- Days with events have colored dots indicator
- Tap date to see events for that day
- Swipe to navigate months

**List View (Alternative):**
- Chronological upcoming events
- Grouped by: Today, Tomorrow, This Week, Later
- Shows event name, time, and icon

#### 4.8.2. Event Types & Icons
**Pre-defined categories:**
- 🔇 Quiet Hours
- 🏠 Apartment Inspection
- 🎉 House Party
- 🧹 Deep Clean Day
- 📦 Package Delivery Expected
- 🔧 Maintenance Visit
- 💰 Rent Due
- 🗑️ Trash Collection Day
- 🎂 Roommate Birthday
- ✈️ Someone Away
- 📝 Custom Event

#### 4.8.3. Add Event Flow
**Bottom sheet form:**
- Event title (text input, required)
- Category selection (icon grid)
- Date picker (required)
- Time picker (optional - all-day events allowed)
- End time (optional)
- Description (optional multi-line)
- Reminder toggle:
  - 1 hour before
  - 1 day before
  - Custom

**Recurring events (Future):**
- Daily, Weekly, Monthly options

#### 4.8.4. Event Notifications
**Automatic reminders:**
- Push notification at configured time
- Shows event title and time
- Tap to open calendar

**Today's Events:**
- Shown on Dashboard in "Next Up" section
- Badge indicator on Calendar tab

---

### 4.9. Settings & Member Management

Comprehensive apartment and user configuration.

#### 4.9.1. Settings Screen Structure
**Accessed via "More" tab in bottom navigation**

**Account Section:**
- Profile picture (Google avatar)
- Display name (Google name)
- Email address
- Language switcher (English / العربية)
- Sign Out button

**Current Apartment Section:**
- Apartment name (tap to rename - admin only)
- Apartment code + copy button
- Share invite link button (Android share sheet)
- Member count badge
- "View Members" → navigates to member management
- "Leave Apartment" (with confirmation)

**Apartment Settings (Admin Only):**
- Hot water buffer duration (10-30 mins slider)
- Trash rotation order management
- Delete apartment (requires typing apartment name for confirmation)

**App Settings:**
- Notification preferences (per-category toggles)
- Data & Storage:
  - Cache size
  - Clear cache button
  - Offline data indicator

**About:**
- App version
- Privacy Policy link
- Terms of Service link
- Open Source Licenses
- Contact Support

#### 4.9.2. Member Management Screen
**Member List:**
- All apartment members with avatars
- Shows role badges: "Admin" / "Member"
- Your entry highlighted

**Each member card shows:**
- Name and email
- Join date
- Current status (Active / Away for trash)
- Actions dropdown (admin only):
  - Promote to Admin
  - Remove from apartment

**Admin Actions:**
- Tap member → bottom sheet with options:
  - **Promote to Admin:** Confirmation dialog, updates role
  - **Remove Member:** Confirmation dialog with warning about their data
- Cannot remove yourself if you're the only admin
- Cannot demote yourself

**Invite New Members:**
- FAB button "Invite Member"
- Shows invite code + share link options
- Copy or share via Android share sheet

#### 4.9.3. Apartment Deletion (Admin Only)
**High-friction flow to prevent accidents:**
1. Navigate to Settings → Delete Apartment
2. Warning dialog:
   - "This will permanently delete [Apartment Name] and ALL data"
   - "All expenses, chores, shower bookings, and history will be lost"
   - "This cannot be undone"
   - Text input: "Type the apartment name to confirm"
3. Validate typed name matches exactly
4. "Delete Permanently" button (red, disabled until validation passes)
5. On confirm:
   - Delete all subcollections (expenses, chores, etc.)
   - Delete apartment document
   - Remove apartment from all members' data
   - Navigate to apartment selection or creation screen

---

### 4.10. Push Notifications

Smart, contextual notifications to keep users informed and engaged.

#### 4.10.1. Notification Categories & Triggers

**Critical (High Priority - Sound + Vibration + Banner):**
1. **Your Shower Starting Soon**
   - Trigger: 5 minutes before your scheduled shower
   - Message: "Your shower starts in 5 minutes 🚿"
   - Action: Tap to open Shower Queue screen

2. **It's Your Turn - Trash**
   - Trigger: When trash rotation moves to you
   - Message: "It's your turn to take out the trash 🗑️"
   - Action: Tap to open Trash Turn screen

**Important (Medium Priority - Silent Banner):**
3. **Expense Added (You're Involved)**
   - Trigger: Someone adds expense where you owe money
   - Message: "[Name] added expense '[Description]' - You owe $[X]"
   - Action: Tap to open expense details

4. **Chore Assigned to You**
   - Trigger: New chore assigned with you as assignee
   - Message: "[Name] assigned you '[Chore Title]' - Due [Date]"
   - Action: Tap to open Chores screen

5. **Shower Queue Update**
   - Trigger: Someone joins/leaves queue when you're in it
   - Message: "[Name] joined the shower queue - You're now #[position]"
   - Action: Tap to open Shower Queue

**Low Priority (Silent Notification):**
6. **Grocery Item Added**
   - Trigger: Someone adds item to grocery list
   - Message: "[Name] added [Item] to the grocery list"
   - Action: Tap to open Groceries screen

7. **Debt Settled**
   - Trigger: Someone marks payment to you as settled
   - Message: "[Name] settled $[X] payment 💰"
   - Action: Tap to open Balances screen

#### 4.10.2. Permission Strategy
**Contextual permission requests:**
- NOT requested on first launch or sign-in
- Requested at point of first relevant action:
  - **First shower booking** → "Get notified when your shower time starts?"
  - **First chore assignment** → "Get notified about new chores?"
  - **Join apartment** → "Stay updated with household activities?"

**Permission Dialog:**
- Custom bottom sheet (not system dialog initially)
- Explains benefits: "Never miss your shower time!"
- "Enable Notifications" button → triggers system permission
- "Not Now" option (can re-request later)

**Handling Denial:**
- If user denies, store preference
- Show in-app prompts instead of push for critical items
- Settings screen shows "Enable Notifications" button → opens app settings

#### 4.10.3. Notification Channels (Android 8.0+)
**Separate channels for granular control:**
- **Shower & Trash (Critical):** Default enabled, sound + vibration
- **Expenses & Chores:** Default enabled, silent
- **Groceries & Calendar:** Default disabled, silent
- **General Updates:** Default disabled

**User can customize:**
- Per-channel enable/disable in Settings
- Sound and vibration preferences
- Do Not Disturb integration

#### 4.10.4. Deep Linking
**All notifications open relevant screen:**
- Shower notification → Shower Queue screen
- Trash notification → Trash Turn screen
- Expense notification → Expense detail modal
- Chore notification → Chores screen (filtered to your chores)

**State preservation:**
- If app is closed, notification launches app → auth check → navigate to target screen
- If app is background, brings to foreground + navigates
- If app is foreground, shows in-app snackbar + optionally navigates

#### 4.10.5. Notification Grouping
**Group by apartment:**
- If user is in multiple apartments, notifications grouped by space
- Expandable notification showing multiple updates
- Summary: "3 updates in [Apartment Name]"

#### 4.10.6. Quiet Hours (Future Enhancement)
**User-configurable silent periods:**
- Set time range (e.g., 10 PM - 7 AM)
- Suppresses non-critical notifications
- Critical shower/trash notifications still show but silent

---

## 5. Technical Architecture & Stack

### 5.1. Frontend Framework & Language
- **Framework:** Flutter 3.24+ (stable channel)
- **Language:** Dart 3.5+
- **Target SDK:** Android API 24 (Android 7.0) to API 35 (Android 15)
- **Compile SDK:** API 35

### 5.2. State Management
- **Primary:** Riverpod 2.5+
  - `flutter_riverpod` for providers
  - `riverpod_annotation` + `riverpod_generator` for code generation
- **Use cases:**
  - Global app state (auth, current apartment)
  - Screen-level state (forms, UI state)
  - Data fetching and caching
  - Real-time Firestore stream providers

### 5.3. Backend & Database
- **Backend:** Firebase (Serverless)
  - **Authentication:** `firebase_auth` (latest stable)
    - Google Sign-In provider only (MVP)
  - **Database:** `cloud_firestore` (latest stable)
    - Real-time listeners for live sync
    - Offline persistence enabled
  - **Push Notifications:** `firebase_messaging` (latest stable)
    - FCM for Android notifications
  - **Storage:** `firebase_storage` (latest stable - future)
  - **Analytics:** `firebase_analytics` (optional)
  - **Crashlytics:** `firebase_crashlytics` (optional)

### 5.4. Local Database (Offline Cache)
- **Package:** `hive` + `hive_flutter`
- **Purpose:**
  - Cache Firestore data for offline access
  - Store user preferences and settings
  - Queue pending writes (expenses, chores) for sync when online
- **Adapters:** Generated via `hive_generator` for freezed models
- **Encryption:** Consider for sensitive data

### 5.5. Routing & Navigation
- **Package:** `go_router`
- **Features:**
  - Declarative routing
  - Deep linking support for notifications
  - Nested navigation for bottom nav tabs
  - Route guards for authentication
- **Structure:**
  - `/` → Splash / Auth Gate
  - `/auth` → Login screen
  - `/onboarding` → First-time setup
  - `/home` → Main app with bottom nav
    - `/home/dashboard`
    - `/home/expenses`
    - `/home/chores`
    - `/home/shower`
    - `/home/more`
  - `/apartment/create` → Create new space
  - `/apartment/join/:code` → Join via invite

### 5.6. Data Modeling & Serialization
- **Immutable Models:** `freezed` + `freezed_annotation`
- **JSON Serialization:** `json_serializable` + `json_annotation`
- **Code Generation:** `build_runner`
- **Benefits:**
  - Type-safe models with equality and copyWith
  - Automatic toJson/fromJson
  - Union types for complex states

**Example Model Structure:**
```dart
@freezed
class Expense with _$Expense {
  factory Expense({
    required String id,
    required String description,
    required double amount,
    required String paidBy,
    required List<ExpenseSplit> splits,
    required DateTime createdAt,
    required bool isSettled,
    String? category,
  }) = _Expense;

  factory Expense.fromJson(Map<String, dynamic> json) => _$ExpenseFromJson(json);
}
```

### 5.7. Internationalization (i18n)
- **Package:** `easy_localization`
- **Supported Languages:**
  - English (en) - LTR
  - Arabic (ar) - RTL
- **Translation Files:** JSON format in `assets/translations/`
  - `en.json`
  - `ar.json`
- **RTL Handling:**
  - Automatic layout mirroring via `Directionality` widget
  - Asset flipping (arrows, icons) based on locale
  - Text alignment adjustments

**Usage:**
```dart
Text('dashboard.greeting'.tr())
Text('expense.you_owe'.tr(namedArgs: {'amount': '45'}))
```

### 5.8. Dependency Injection
- **Approach:** No dedicated DI package - use Riverpod providers
- **Service Registration:**
  - FirebaseAuth instance via provider
  - Firestore instance via provider
  - Repository pattern with provider family
  - Scoped providers for screen-level dependencies

### 5.9. Animations & UI
**Animation Packages (AI will decide best option):**
- Options: `flutter_animate`, `lottie`, or native Flutter animations
- **Use for:**
  - Page transitions
  - Completion animations (checkmark, confetti)
  - Loading states (shimmer)
  - Micro-interactions (button press, card tap)

**UI Components:**
- **Icons:** `lucide_icons_flutter` (consistent with web version)
- **Loading Indicators:** Custom shimmer or `shimmer` package
- **Modals:** Custom bottom sheets with `modal_bottom_sheet`
- **Toast/Snackbar:** Native SnackBar or `fluttertoast`
- **Swipe Actions:** `flutter_slidable`

### 5.10. Additional Packages
- **Date/Time:** `intl` (included with easy_localization)
- **Image Handling:** `cached_network_image` (for avatars)
- **URL Launcher:** `url_launcher` (for invite links, support)
- **Share:** `share_plus` (for invite links, export)
- **Device Info:** `device_info_plus` (for compatibility checks)
- **Connectivity:** `connectivity_plus` (offline detection)
- **Permission Handler:** `permission_handler` (notification permissions)

### 5.11. Development Tools
- **Linting:** `flutter_lints` + custom lint rules
- **Code Generation:** `build_runner` for freezed, json_serializable, riverpod
- **Firebase CLI:** For emulators and deployment
- **Android Studio / VS Code** with Flutter extensions

---

## 6. Design System Specification

### 6.1. Color Palette

#### Brand Colors
```dart
// Primary Blue
static const primaryBase = Color(0xFF2563EB);
static const primaryDark = Color(0xFF1D4ED8);
static const primaryLight = Color(0xFFDBEAFE);

// Secondary Indigo
static const secondaryBase = Color(0xFF6366F1);
static const secondaryDark = Color(0xFF4F46E5);
static const secondaryLight = Color(0xFFE0E7FF);
```

#### Semantic Colors
```dart
// Success Green
static const successBase = Color(0xFF10B981);
static const successDark = Color(0xFF059669);
static const successLight = Color(0xFFD1FAE5);

// Danger Red
static const dangerBase = Color(0xFFEF4444);
static const dangerDark = Color(0xFFDC2626);
static const dangerLight = Color(0xFFFEE2E2);

// Warning Amber
static const warningBase = Color(0xFFF59E0B);
static const warningDark = Color(0xFFD97706);
static const warningLight = Color(0xFFFEF3C7);

// Info Sky
static const infoBase = Color(0xFF0EA5E9);
static const infoDark = Color(0xFF0284C7);
static const infoLight = Color(0xFFE0F2FE);
```

#### Backgrounds & Surfaces
```dart
static const appBackground = Color(0xFFF8FAFC);
static const surface = Color(0xFFFFFFFF);
static const cardBorder = Color(0xFFF1F5F9);
static const darkBackground = Color(0xFF020617); // For dark mode (future)
```

#### Typography Colors
```dart
static const textPrimary = Color(0xFF0F172A);
static const textSecondary = Color(0xFF475569);
static const textMuted = Color(0xFF94A3B8);
```

### 6.2. Typography System

#### Font Families
- **Latin/English:** Outfit (Google Fonts)
- **Arabic:** Tajawal (Google Fonts)

**Implementation:**
```yaml
# pubspec.yaml
dependencies:
  google_fonts: ^6.2.1
```

#### Text Styles
```dart
class AppTextStyles {
  // Page Title Large (Dashboard Greeting)
  static TextStyle pageTitleLarge(bool isArabic) => TextStyle(
    fontFamily: isArabic ? GoogleFonts.tajawal().fontFamily : GoogleFonts.outfit().fontFamily,
    fontSize: 24,
    fontWeight: FontWeight.w800,
    letterSpacing: -0.5,
    color: AppColors.textPrimary,
  );

  // Page Title (Standard Headers)
  static TextStyle pageTitle(bool isArabic) => TextStyle(
    fontFamily: isArabic ? GoogleFonts.tajawal().fontFamily : GoogleFonts.outfit().fontFamily,
    fontSize: 18,
    fontWeight: FontWeight.w800,
    letterSpacing: -0.5,
    color: AppColors.textPrimary,
  );

  // Section Title
  static TextStyle sectionTitle(bool isArabic) => TextStyle(
    fontFamily: isArabic ? GoogleFonts.tajawal().fontFamily : GoogleFonts.outfit().fontFamily,
    fontSize: 14,
    fontWeight: FontWeight.w700,
    color: AppColors.textPrimary,
  );

  // Page Subtitle
  static TextStyle pageSubtitle(bool isArabic) => TextStyle(
    fontFamily: isArabic ? GoogleFonts.tajawal().fontFamily : GoogleFonts.outfit().fontFamily,
    fontSize: 10,
    fontWeight: FontWeight.w500,
    height: 1.5,
    color: AppColors.textSecondary,
  );

  // Body Text
  static TextStyle body(bool isArabic) => TextStyle(
    fontFamily: isArabic ? GoogleFonts.tajawal().fontFamily : GoogleFonts.outfit().fontFamily,
    fontSize: 14,
    fontWeight: FontWeight.w500,
    color: AppColors.textPrimary,
  );

  // Caption / Badge
  static TextStyle caption(bool isArabic) => TextStyle(
    fontFamily: isArabic ? GoogleFonts.tajawal().fontFamily : GoogleFonts.outfit().fontFamily,
    fontSize: 10,
    fontWeight: FontWeight.w700,
    letterSpacing: 1.0,
    color: AppColors.textPrimary,
  );
}
```

### 6.3. Border Radii
```dart
class AppBorderRadius {
  static const small = 8.0;      // Standard buttons, small inputs
  static const medium = 12.0;    // List items, inner elements
  static const large = 16.0;     // Main widgets, standard cards
  static const xLarge = 24.0;    // Modals, bottom sheets
  static const xxLarge = 40.0;   // Auth cards, large containers
}
```

### 6.4. Gradients
```dart
class AppGradients {
  // Primary Card Gradient
  static const primaryCard = LinearGradient(
    begin: Alignment.topLeft,
    end: Alignment.bottomRight,
    colors: [
      Color(0xFF2563EB), // primaryBase
      Color(0xFF60A5FA), // lighter blue
    ],
  );

  // Secondary Card Gradient
  static const secondaryCard = LinearGradient(
    begin: Alignment.topLeft,
    end: Alignment.bottomRight,
    colors: [
      Color(0xFF475569), // gray-600
      Color(0xFF94A3B8), // gray-400
    ],
  );
}
```

### 6.5. Shadows & Elevations
```dart
class AppShadows {
  // Standard Card Shadow
  static List<BoxShadow> card = [
    BoxShadow(
      color: Colors.black.withOpacity(0.05),
      offset: const Offset(0, 4),
      blurRadius: 10,
    ),
  ];

  // Primary Button / Active Shadow
  static List<BoxShadow> primaryButton = [
    BoxShadow(
      color: const Color(0xFF2563EB).withOpacity(0.20),
      offset: const Offset(0, 8),
      blurRadius: 15,
    ),
  ];
}
```

### 6.6. Spacing System
**8-point grid system:**
```dart
class AppSpacing {
  static const xs = 4.0;
  static const sm = 8.0;
  static const md = 16.0;
  static const lg = 24.0;
  static const xl = 32.0;
  static const xxl = 48.0;
}
```

### 6.7. Component Specifications

#### Button Styles
**Primary Button:**
- Background: `primaryBase` gradient
- Text: White, 14px, Bold (700)
- Height: 48px
- Border Radius: `small` (8px)
- Shadow: `primaryButton`
- Padding: Horizontal 24px
- Tap animation: Scale to 0.95

**Secondary Button:**
- Background: Transparent
- Border: 2px solid `primaryBase`
- Text: `primaryBase`, 14px, Bold (700)
- Height: 48px
- Border Radius: `small` (8px)

**Text Button:**
- Background: Transparent
- Text: `primaryBase`, 14px, SemiBold (600)
- No border

#### Card Components
**Standard Card:**
- Background: `surface` (white)
- Border: 1px solid `cardBorder`
- Border Radius: `large` (16px)
- Shadow: `card`
- Padding: 16px

**Stat Card (Dashboard):**
- Background: Gradient (`primaryCard`)
- Border Radius: `large` (16px)
- Shadow: `card`
- Text: White for all elements
- Icon: 32x32px, white with 20% opacity background circle

**List Item Card:**
- Background: `surface`
- Border: 1px solid `cardBorder`
- Border Radius: `medium` (12px)
- Padding: 12px 16px
- Tap: Ripple effect with `primaryLight`

#### Input Fields
- Height: 48px
- Border: 1.5px solid `cardBorder`
- Border Radius: `small` (8px)
- Focus Border: 2px solid `primaryBase`
- Padding: Horizontal 16px
- Font: Body text style
- Placeholder: `textMuted`

#### Bottom Sheets
- Border Radius: Top corners only, `xLarge` (24px)
- Background: `surface`
- Handle: 4px tall, 40px wide, rounded, `cardBorder` color
- Padding: 24px horizontal, 16px top (below handle)

### 6.8. Animation Guidelines
**Micro-interactions:**
- Button press: Scale down to 0.95, duration 100ms
- Card tap: Ripple effect with primary color at 10% opacity
- Page transitions: Fade + slide (300ms ease-out)
- Modal open: Slide up from bottom (350ms cubic bezier)

**Loading States:**
- Skeleton shimmer: Gradient animation, 1.5s duration
- Spinner: Primary color, 1s rotation
- Progress indicators: Smooth linear progress with primary color

**Success Animations:**
- Checkmark: Draw animation (500ms) + scale bounce
- Completion: Confetti burst (1s) for significant actions (trash done, chore complete)

### 6.9. Gorgeous UI Enhancement Guidelines

**To make the app stunning:**

1. **Smooth Transitions Everywhere:**
   - All screen changes have fade + slide animations
   - List items animate in with stagger effect
   - Modals slide up with bounce easing

2. **Delightful Micro-Interactions:**
   - Buttons scale slightly on press
   - Checkboxes bounce when checked
   - Success actions show confetti or celebration animation
   - Loading states use elegant shimmer, not spinners

3. **Visual Hierarchy:**
   - Use gradients on important CTAs and stat cards
   - Hero elements (shower status, current trash turn) are large and prominent
   - White space is generous - never cramped
   - Cards have subtle shadows and borders for depth

4. **Motion Design:**
   - Icons pulse when active (shower available, your turn)
   - Countdown timers animate smoothly
   - Progress bars fill smoothly, not in jumps
   - Tab switching has cross-fade + scale

5. **Empty States:**
   - Beautiful illustrations (or simple icons with gradients)
   - Encouraging messages: "No expenses yet! Add your first bill."
   - Clear CTAs to get started

6. **Error States:**
   - Friendly, non-technical error messages
   - Retry buttons styled consistently
   - Offline mode shows elegant banner, not intrusive alert

7. **Accessibility with Beauty:**
   - High contrast text on all backgrounds
   - Large touch targets (48dp minimum)
   - RTL layouts mirror perfectly, including animations

8. **Liquid Glass Bottom Navigation Bar**

---

## 7. Firebase Firestore Data Model

### 7.1. Collection Structure

**Note:** This is a simplified representation. The actual structure will be implemented in the `firestore-blueprint.json` file.

```
/apartments (collection)
  /{apartmentId} (document)
    - name: string
    - code: string (6-char unique)
    - createdAt: timestamp
    - createdBy: string (userId)
    - settings: map
      - hotWaterBuffer: number (minutes, default: 20)
    
    /members (subcollection)
      /{userId} (document)
        - uid: string
        - email: string
        - displayName: string
        - photoURL: string
        - role: string ("admin" | "member")
        - joinedAt: timestamp
        - isAway: boolean (for trash rotation)
    
    /expenses (subcollection)
      /{expenseId} (document)
        - description: string
        - amount: number
        - paidBy: string (userId)
        - category: string (optional)
        - createdAt: timestamp
        - isSettled: boolean
        - splits: array of maps
          - userId: string
          - amount: number
          - settled: boolean
        - settledAt: timestamp (optional)
        - settledBy: string (optional)
    
    /chores (subcollection)
      /{choreId} (document)
        - title: string
        - description: string (optional)
        - assignedTo: string (userId)
        - dueDate: timestamp (optional)
        - isCompleted: boolean
        - completedAt: timestamp (optional)
        - completedBy: string (optional)
        - createdAt: timestamp
        - createdBy: string (userId)
    
    /trash_rotation (subcollection)
      /settings (document)
        - rotationOrder: array of userIds
        - currentIndex: number
      
      /history (subcollection)
        /{historyId} (document)
          - userId: string
          - action: string ("done" | "override" | "skip")
          - timestamp: timestamp
          - rotationIndex: number (optional)
    
    /shower_queue (subcollection)
      /{bookingId} (document)
        - userId: string
        - startTime: timestamp
        - duration: number (minutes)
        - isActive: boolean
        - endTime: timestamp (computed)
        - createdAt: timestamp
    
    /groceries (subcollection)
      /{itemId} (document)
        - itemName: string
        - quantity: string (optional)
        - notes: string (optional)
        - addedBy: string (userId)
        - addedAt: timestamp
        - isPurchased: boolean
        - purchasedBy: string (optional)
        - purchasedAt: timestamp (optional)
    
    /calendar (subcollection)
      /{eventId} (document)
        - title: string
        - category: string
        - date: timestamp
        - time: string (optional)
        - endTime: string (optional)
        - description: string (optional)
        - createdBy: string (userId)
        - createdAt: timestamp

/users (collection)
  /{userId} (document)
    - uid: string
    - email: string
    - displayName: string
    - photoURL: string
    - createdAt: timestamp
    - apartments: array of apartmentIds
    - fcmToken: string (for push notifications)
    - preferences: map
      - language: string ("en" | "ar")
      - notifications: map
        - shower: boolean
        - trash: boolean
        - expenses: boolean
        - chores: boolean
        - groceries: boolean
```

### 7.2. Firestore Security Rules

**Note:** These rules are for reference only. The actual rules will be implemented in the `firestore.rules` file.

```javascript
rules_version = '2';
service cloud.firestore {
  match /databases/{database}/documents {
    
    // Helper functions
    function isAuthenticated() {
      return request.auth != null;
    }
    
    function isApartmentMember(apartmentId) {
      return isAuthenticated() && 
             exists(/databases/$(database)/documents/apartments/$(apartmentId)/members/$(request.auth.uid));
    }
    
    function isApartmentAdmin(apartmentId) {
      return isAuthenticated() && 
             get(/databases/$(database)/documents/apartments/$(apartmentId)/members/$(request.auth.uid)).data.role == 'admin';
    }
    
    // Users collection
    match /users/{userId} {
      allow read: if isAuthenticated() && request.auth.uid == userId;
      allow create: if isAuthenticated() && request.auth.uid == userId;
      allow update: if isAuthenticated() && request.auth.uid == userId;
      allow delete: if false;
    }
    
    // Apartments collection
    match /apartments/{apartmentId} {
      allow read: if isApartmentMember(apartmentId);
      allow create: if isAuthenticated();
      allow update: if isApartmentAdmin(apartmentId);
      allow delete: if isApartmentAdmin(apartmentId);
      
      // Members subcollection
      match /members/{userId} {
        allow read: if isApartmentMember(apartmentId);
        allow create: if isAuthenticated();
        allow update: if isApartmentAdmin(apartmentId) || request.auth.uid == userId;
        allow delete: if isApartmentAdmin(apartmentId);
      }
      
      // Expenses subcollection
      match /expenses/{expenseId} {
        allow read: if isApartmentMember(apartmentId);
        allow create: if isApartmentMember(apartmentId);
        allow update: if isApartmentMember(apartmentId);
        allow delete: if isApartmentMember(apartmentId);
      }
      
      // Chores subcollection
      match /chores/{choreId} {
        allow read: if isApartmentMember(apartmentId);
        allow create: if isApartmentMember(apartmentId);
        allow update: if isApartmentMember(apartmentId);
        allow delete: if isApartmentMember(apartmentId);
      }
      
      // Trash rotation
      match /trash_rotation/{document=**} {
        allow read: if isApartmentMember(apartmentId);
        allow write: if isApartmentMember(apartmentId);
      }
      
      // Shower queue
      match /shower_queue/{bookingId} {
        allow read: if isApartmentMember(apartmentId);
        allow create: if isApartmentMember(apartmentId);
        allow update: if isApartmentMember(apartmentId) && 
                         (request.auth.uid == resource.data.userId || isApartmentAdmin(apartmentId));
        allow delete: if request.auth.uid == resource.data.userId || isApartmentAdmin(apartmentId);
      }
      
      // Groceries
      match /groceries/{itemId} {
        allow read: if isApartmentMember(apartmentId);
        allow create: if isApartmentMember(apartmentId);
        allow update: if isApartmentMember(apartmentId);
        allow delete: if isApartmentMember(apartmentId);
      }
      
      // Calendar
      match /calendar/{eventId} {
        allow read: if isApartmentMember(apartmentId);
        allow create: if isApartmentMember(apartmentId);
        allow update: if isApartmentMember(apartmentId);
        allow delete: if isApartmentMember(apartmentId);
      }
    }
  }
}
```

### 7.3. Firestore Indexes

**Required Composite Indexes:**

```javascript
// Expenses - for filtering and sorting
apartments/{apartmentId}/expenses
  - isSettled (Ascending), createdAt (Descending)
  - paidBy (Ascending), createdAt (Descending)

// Chores - for filtering by completion and due date
apartments/{apartmentId}/chores
  - isCompleted (Ascending), dueDate (Ascending)
  - assignedTo (Ascending), isCompleted (Ascending), dueDate (Ascending)

// Shower Queue - for chronological ordering
apartments/{apartmentId}/shower_queue
  - isActive (Descending), startTime (Ascending)

// Groceries - for active items
apartments/{apartmentId}/groceries
  - isPurchased (Ascending), addedAt (Descending)
```

---

## 8. Offline-First Architecture

### 8.1. Strategy Overview
**Goal:** App remains fully functional without internet, syncing changes when connectivity returns.

**Approach:**
1. **Firestore Offline Persistence:** Enabled by default
2. **Hive Local Cache:** Redundant layer for critical data
3. **Write Queue:** Pending actions stored locally, synced on reconnection

### 8.2. Firestore Offline Persistence
**Configuration:**
```dart
FirebaseFirestore.instance.settings = const Settings(
  persistenceEnabled: true,
  cacheSizeBytes: Settings.CACHE_SIZE_UNLIMITED,
);
```

**Behavior:**
- Firestore automatically caches all read documents
- Writes go to local cache first, then sync to server
- Real-time listeners work with cached data when offline
- Server data overwrites local on reconnection

### 8.3. Hive Implementation

**Use Cases:**
- User preferences (language, notification settings)
- Current apartment selection
- Pending write queue (for reliability)
- Quick-access data (stats, balances)

**Box Structure:**
```dart
// User Preferences Box
Box<UserPreferences>: 'user_prefs'
  - language: String
  - currentApartmentId: String
  - notificationSettings: Map

// Pending Actions Box
Box<PendingAction>: 'pending_actions'
  - action: String (add_expense, complete_chore, etc.)
  - data: Map
  - timestamp: DateTime
  - retryCount: int

// Cached Stats Box
Box<CachedStats>: 'stats_cache'
  - apartmentId: String
  - stats: Map
  - lastUpdated: DateTime
```

**Sync Logic:**
```dart
// On app start or connectivity change
if (isOnline) {
  final pendingActions = Hive.box<PendingAction>('pending_actions');
  for (var action in pendingActions.values) {
    try {
      await executeAction(action);
      await pendingActions.delete(action.key);
    } catch (e) {
      action.retryCount++;
      if (action.retryCount > 5) {
        showFailureNotification(action);
      }
    }
  }
}
```

### 8.4. Connectivity Detection
**Package:** `connectivity_plus`

**Provider:**
```dart
final connectivityProvider = StreamProvider<ConnectivityResult>((ref) {
  return Connectivity().onConnectivityChanged;
});
```

**UI Indicators:**
- **Online:** No indicator
- **Offline:** Small banner at top: "Offline - Changes will sync when connected"
- **Syncing:** Snackbar: "Syncing changes..."

---

## 9. Navigation & Information Architecture

### 9.1. Bottom Navigation Structure

**5 Main Tabs:**
1. **Dashboard** 🏠 - Icon: `home` - Route: `/home/dashboard`
2. **Expenses** 💰 - Icon: `receiptText` - Route: `/home/expenses`
3. **Chores** ✓ - Icon: `checkSquare` - Route: `/home/chores`
4. **Shower** 🚿 - Icon: `droplet` - Route: `/home/shower`
5. **More** ⋮ - Icon: `menu` - Route: `/home/more` (Drawer Menu)

### 9.2. "More" Drawer Menu

**Household Management:**
- Trash Turn 🗑️
- Groceries 🛒
- Calendar 📅

**Apartment Settings:**
- Members 👥
- Balances 💵
- Invite Friends 📨

**Personal:**
- Profile & Preferences ⚙️
- Notifications 🔔
- Language 🌐

**Support:**
- Help & Support
- About
- Sign Out

### 9.3. Deep Linking
- `app://join/{code}` → Join apartment
- `app://shower` → Shower queue
- `app://trash` → Trash turn
- `app://expense/{id}` → Expense details
- `app://chore/{id}` → Chore details

---

## 10. Non-Functional Requirements

### 10.1. Performance
- Cold start < 3 seconds
- 60 FPS animations
- Real-time updates within 1 second
- Zero delay on cached data

### 10.2. Real-Time Synchronization
- All data syncs instantly via Firestore `onSnapshot`
- No pull-to-refresh required

### 10.3. Security
- Firebase Auth with Google Sign-In
- Email/Password Sign-In
- Reset password
- Strict Firestore security rules (see in firestore.rules file)
- Client-side + server-side validation

### 10.4. Accessibility
- Full RTL support for Arabic
- WCAG AA color contrast
- 48dp minimum touch targets
- Semantic labels for screen readers

### 10.5. Localization
- English and Arabic
- Localized date/time/number formatting
- Currency symbols

---

## 11. Development Phases

### Phase 1: Foundation (Weeks 1-2)
- Flutter setup with all packages
- Firebase configuration
- Design system implementation
- Authentication flow
- Bottom nav scaffold

### Phase 2: Core Features (Weeks 3-5)
- Dashboard with real-time data
- Expense tracking
- Chore management
- Trash rotation
- Firestore models with freezed

### Phase 3: Resource Management (Weeks 6-7)
- Shower queue
- Grocery list
- Shared calendar
- Member management
- Settings

### Phase 4: Polish & Notifications (Weeks 8-9)
- Push notifications
- Deep linking
- Animations
- Error handling
- RTL support

### Phase 5: Testing & Launch (Weeks 10-12)
- Manual testing
- Beta release
- Bug fixes
- Production release

---

## 12. Success Metrics

- **DAU:** 70% of users
- **Crash-free rate:** > 99.5%
- **Day 1 retention:** > 70%
- **App rating:** 4.5+ stars

---

## 13. Future Enhancements

- Payment integration (Stripe/PayPal)
- Recurring expenses
- Receipt scanning (OCR)
- Recurring chores
- Voice input
- Data insights & analytics
- Tablet optimization
- Dark mode

---

**Document End**

*This PRD is ready for AI-assisted development with the specified tech stack.*