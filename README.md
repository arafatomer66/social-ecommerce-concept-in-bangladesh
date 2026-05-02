# ShareDeal Social — Feature Flows

> **Audience:** the dev team building the production version of this prototype.
> **What this is:** the canonical UX spec for every screen + every interaction in the Flutter prototype at `lib/v6/`. The visual rendering and screen-to-screen flow shown in the prototype IS the brief. Any backend wiring choices (state shape, API contract, escrow rules) called out below are recommendations.
> **What this is not:** a tech-stack mandate. Use whichever stack works — but the user-visible behavior should match.

---

## 0. Stack at a glance

| Layer | Prototype (mock-only) | Production target |
|---|---|---|
| App | Flutter 3.10+ / GetX state | Same |
| Auth | Mocked OTP, any 6 digits | Phone OTP via SMS, 6-digit, 5-min TTL, 5 attempts |
| Backend | None — `lib/v6/data/sd_data.dart` | Laravel 12 + MySQL (already scaffolded at `~/Desktop/sharedeal-social/api`) |
| Storage | GetStorage (token, prefs) | Same client-side; backend stores user data |
| Payments | Mocked bKash popup, COD radio | bKash SDK + COD; Nagad/card optional |
| SMS | None | BulkSMS BD (already wired in legacy) |
| i18n | Bengali primary, English toggle | Same — every string goes through `t(bn, en)` |

---

## 1. App-wide structure

### 1.1 Bottom-nav (5 tabs, persistent)
Source: `lib/v6/widgets/sd_bottom_nav.dart`, hosted by `lib/v6/screens/v6_shell.dart`.

| Tab | Icon | Screen | Purpose |
|---|---|---|---|
| Home | 🏠 | `V6HomeScreen` | Discovery (banners, categories, flash, groups, live) |
| Explore | 🔍 | `V6ExploreScreen` | Search + browse + sort + filter |
| Social | 📡 | `V6SocialScreen` | Feed of friends/nearby groups + follow toggle |
| Cart | 🛒 | `V6CartScreen` | Cart → address → payment → success (badge shows live count) |
| Profile | 👤 | `V6ProfileScreen` | User, orders, coins, referrals, games, settings |

Detail/flow screens (product detail, group chat, live stream, bargain, etc.) push onto an **overlay stack** that keeps the bottom nav visible — except for immersive screens (live stream, bargain, eid salami) which take over the full viewport.

### 1.2 Onboarding gate (cold start, first run only)

Order: **Splash (2s) → 4 colored swipeable slides → Phone → OTP → Profile**.

Reference impl: `lib/v6/auth/v6_login_flow.dart`. Pixel-faithful port of the v6 HTML mockup (`sd-onboarding.jsx`).

- **Splash** — Green gradient bg, 🛒 logo, "ShareDeal" wordmark in yellow, tagline "শেয়ার করুন · বাঁচান · জিতুন", animated 3-dot pulse. Auto-advances after 2s.
- **Slide 1** (green #006A4E): 👥 "একসাথে কিনুন, বেশি বাঁচান!" — group buying value prop
- **Slide 2** (red #C62828): ⚡ "প্রতিদিন ফ্ল্যাশ সেল!" — flash deals up to 70% off
- **Slide 3** (orange #FF6D00): 🎡 "কেনাকাটা করুন, পুরস্কার জিতুন!" — gamification rewards
- **Slide 4** (blue #1565C0): 📍 "কাছের মানুষ, কাছের দাম!" — local groups
- Yellow indicator dots (active wide, others narrow)
- Yellow CTA: "পরবর্তী →" / "শুরু করুন 🚀" on last slide
- "এড়িয়ে যান" (Skip) link below CTA on slides 1–3
- **Phone screen**: green gradient header, 🇧🇩 +880 country prefix, 11-digit input, social-login buttons (Facebook/Google) below
- **OTP screen**: 6-digit boxes, "Demo: enter any 6 digits ✅" hint, resend timer
- **Profile setup**: photo placeholder, name + area inputs, welcome bonus card "৳50 cashback + 50 coins"

After Profile → "ShareDeal শুরু করুন 🚀" → V6Shell home.

`Pref.write(KeyWord.onboardingDone, true)` is set so subsequent launches skip onboarding.

### 1.3 Languages
EN ↔ BN toggle in every screen's top bar (`SdLangToggle` widget). Persists in GetStorage. All strings go through `t(bn, en)` from `lib/v6/i18n/sd_lang.dart`.

---

## 2. Home tab (`home_screen.dart`)

The discovery hub. Scrollable feed:

### 2.1 Top bar
- Search pill (taps → Explore tab)
- Lang toggle
- 🔔 Notifications (with red unread dot) → push notifications screen

### 2.2 Banner carousel
- 3 auto-rotating banners (3s interval), tappable dots
- "এখনই দেখুন →" CTA per banner routes to flash / profile / explore

### 2.3 Category strip
8 horizontal-scroll category chips: চাল-ডাল, সবজি, মাছ-মাংস, তেল-মশলা, দুগ্ধজাত, স্ন্যাকস, ক্লিনিং, আরও. Tap → Explore filtered.

### 2.4 Flash deals carousel
- Section header "⚡ ফ্ল্যাশ সেল" + countdown + "সব দেখুন →"
- 4 product cards; tap → product detail in flash mode
- Each card: image, name, dealPrice (red), originalPrice (strike), discount%, sold-progress bar, countdown

### 2.5 Group deals carousel
Similar layout. Tap card → product detail in group mode. Each card has a "Join & Share" button that opens the invite flow.

### 2.6 Live streams strip
3-5 live cards. Tap → live stream viewer.

### 2.7 Gamification CTAs
Cards for Para Neta (leaderboard), For You (personal feed), Orchard, Games (spin/scratch), Bargain. Tap → respective screen.

### 2.8 WhatsApp-acquisition demo strip
Test cards for landing screens (Bargain link, Eid Salami link, Group chat preview). Demo only — devs can hide in prod.

---

## 3. Product detail (`product_screen.dart`)

The richest screen. Sections top-to-bottom:

### 3.1 Top bar
- Back arrow, lang toggle, cart icon (with badge)
- Hero image (square aspect, with discount % badge top-left, ❤️/🤍 wishlist toggle top-right)
- ❤️ tap → toggles `WishlistStore.to.toggle(productId)` + snackbar

### 3.2 Mode toggle (Solo / Group)
Top of details — 2 tabs. Default = Group for group/flash products, Solo for explore.

### 3.3 Price block (Group mode)
- Price comparison: solo price (struck through), group price (large green), discount %, savings
- Group filling progress (current/target) with avatar pile
- Live countdown timer

### 3.4 Tiered pricing card 🎯
"যত বেশি বন্ধু, তত কম দাম" — More friends, lower price.

3 tiles:
| Tile | Style | Header | Price | Bottom |
|---|---|---|---|---|
| 1 (active) | white bg, solid green border | "৩ জন" green | green ৳299 | "সাশ্রয় ৳181" muted |
| 2 (mid) | cream bg, **dashed orange border** | "৫ জন" orange | orange ৳269 | "সাশ্রয় ৳211" muted |
| 3 (best) | red bg, solid yellow border, **🔥 সেরা floating chip top-right** | "১০ জন" white | yellow ৳233 | "সাশ্রয় ৳247" white-translucent |

### 3.5 Subscribe & Save
**Visible only on subscription-eligible SKUs** (groceries — rice, oil, milk, eggs, dal, ghee, sugar, salt, biscuits, water). Hidden for fish/meat/seasonal.

Cadence picker (weekly / biweekly / monthly), subscribe price (~10% off solo). Tap → confirmation bottom sheet.

### 3.6 Open Groups list
"৩টি গ্রুপ চলছে — যোগ দিন!"

Filtered by **scope** picker (only 2 options to keep it simple):
- 🏢 আমার বিল্ডিং (My building)
- 🏘️ আমার পাড়া (My neighborhood)

Each row: leader avatar, leader name, locality tag, joined-count, time-remaining countdown, a "যোগ দিন" tap target (selecting it sets `_selectedGroupId` and changes the bottom CTA from "নতুন গ্রুপ" to "যোগ দিন").

### 3.7 Verified Seller card
"🏪 ধান সিদ্ধ ফার্মস ✓ যাচাইকৃত · NID যাচাই · ০ অভিযোগ"

Tap → push **merchant storefront** (`merchant_store_screen.dart`).

Below: green escrow chip "আপনার টাকা সুরক্ষিত (Escrow) — ৭ দিনের ফেরত গ্যারান্টি"

### 3.8 Delivery row
3 chips: 🚚 দ্রুত ডেলিভারি, ↩️ রিটার্ন, ✅ গ্যারান্টি.

### 3.9 Tabs
**বিবরণ** (Description) | **রিভিউ** (Reviews — list of 3 mock reviews with stars) | **প্রশ্ন** (Q&A — list of 3 mock Q&As + ask-form that posts inline)

### 3.10 Bottom CTA bar (sticky)

**Solo mode** (3 buttons):
1. 👥 গ্রুপে -৳N (light green, switches to group mode)
2. 🛒 কার্টে যোগ (orange, adds to CartStore + snackbar with View Cart)
3. ⚡ এখনই কিনুন · ৳N (red, adds + jumps to cart)

**Group mode** (3 buttons):
1. ✂️ ফ্রি জিতুন (yellow gradient, → Bargain screen with this product)
2. 💬 প্রশ্ন (light green, → switches to Q&A tab)
3. 🛒 যোগ দিন · ৳N (green, depending on `_selectedGroupId` either joins existing or pushes group-create flow)

---

## 4. Group buying core flow

The hero feature. End-to-end:

```
Browse product → "যোগ দিন" tapped
   ↓
   IF an open group selected → CartStore.add(isGroup=true) → Cart screen "Joined Group" success
   ELSE → Group Create screen (pick scope, target size, duration) → Cart "Group Started" success
   ↓
Cart success screen shows:
   • ✓ Order placed
   • 🛡️ Escrow active
   • ⏳ "Auto-refund if group does not fill in 24h" (with the ৳ amount)
   ↓
Profile → My Groups → Active tab shows it filling
   ↓
When group fills:
   notifications: "🎉 Group filled! Order confirmed"
   Tracking: starts processing → packed → out for delivery → delivered
   ↓
On delivery:
   Notifications: scratch card unlocked
   Order detail offers: "↩️ Request return" within 7 days
```

Key trust copy throughout: **"গ্রুপ পূর্ণ না হলে অটো-রিফান্ড"** — always with timer + ৳ amount.

---

## 5. Cart → checkout → success (`cart_screen.dart`)

4 steps, single screen with state machine `CartStep` enum:

### 5.1 Cart step
- Item rows (image, name, isGroup tag, qty +/- buttons, line total, remove × button)
- Coupon input + Apply button (mock — accepts any code, applies ৳30 off)
- Price summary (subtotal, deal savings, coupon discount, delivery, **total**)
- "এগিয়ে যান →" CTA

### 5.2 Address step
- List of saved addresses (from `AddressesApi` or fallback)
- "+ নতুন ঠিকানা" → expandable form with:
  - **Map preview** (faux Dhanmondi grid + 📍 pin) — Bangladesh addressing is landmark-driven
  - Flat# + Road/Lane (side-by-side)
  - **Landmark (required)** — e.g. "রাপা প্লাজার পেছনে"
  - Area / Neighborhood
- "Save" composes them into a single line and persists locally

### 5.3 Payment step
3 radio options:
- 💵 ক্যাশ অন ডেলিভারি (default)
- 📱 bKash
- 💳 Nagad / কার্ড (placeholder)

CoD-specific toggle: "Confirm with a phone call before delivery"

### 5.4 Place order
- COD → straight to success (700ms simulated processing)
- bKash → **3-step popup**:
  1. PIN entry (5 digits, masked) + amount + Cancel
  2. Verifying spinner (~1.4s)
  3. ✓ Confirmed with mock TrxID (BKS-XXXXXX)
- On confirm → success step, order appended to `OrderStore`, cart cleared

### 5.5 Success step
- ✅ Big check, "অর্ডার সফল হয়েছে!" / "Group Started!" / "Joined the Group!"
- Order number, ETA, escrow chip
- **Group refund promise** card (yellow): "২৪ ঘণ্টায় X জন না হলে আপনার ৳T ওয়ালেটে ফেরত যাবে"
- bKash verify card (if applicable)
- Eid Salami unlock card with "Send to friends" CTA → eid salami screen
- Track Order CTA → tracking screen with the new order ID
- "পরে দেখব · হোমে ফিরুন" link

---

## 6. Tracking + returns + cancel (`tracking_screen.dart`)

Single screen:
- **Map preview** with rider position interpolating from origin to destination
- **Rider card** (name, phone CTA, ETA)
- **Timeline**: placed → packed → rider picked up → out for delivery → delivered (icons + timestamps + active stage highlighted)
- **Order summary** (id, item, price)

Conditional CTAs:
- **If status = delivered**: "↩️ রিটার্ন/রিফান্ড চান?" yellow card — opens return-request bottom sheet:
  - Reason radio: damaged / wrong item / quality / not as described / other
  - Description textarea
  - "Add photos" placeholder
  - Submit → snackbar "✅ Return sent · response within 24h"
- **If status = placed/processing/packed** (within 30-min window): "🛑 Cancel order" red card — opens cancel sheet:
  - Reason radio: changed mind / found cheaper / delivery slow / wrong address / other
  - Confirm → snackbar "✅ Cancelled · refund to wallet in 48h" + back

---

## 7. Bargain "Win Free" (`bargain_screen.dart`)

Friend-help-cuts-the-price game. Pinduoduo-style.

- Hero card: large "৳XXX" current price (animated cut-amount pop on each tap), countdown
- Helpers list (top 6) with avatars + ৳ contributed
- Progress bar: how close to FREE
- "✂️ Tap to help cut" button (auto-decrements price, adds a helper)
- Share strip: WhatsApp / Facebook / SMS / Copy → toast + auto-simulates a friend-help (so demo feels alive)
- Reward ladder: shows next milestone (50% off → 75% off → FREE!)
- When ≥100% reached → modal with "🎉 Won FREE!" + Claim button → cart in join mode

**Tuning:** helpers needed = 3 (down from 8) so single demo user can win solo. Edit `_friendsNeeded` getter to change.

---

## 8. Eid Salami (formerly "Red Envelope") — `red_envelope_screen.dart`

Cultural rebrand: Bangladesh's Eid gift-giving tradition. Mechanic = lucky-money grab.

Trigger: post-purchase success card "🧧 Send Eid Salami on WhatsApp" → opens screen.

- Hero pool meter: total ৳ + 8-slot grid (envelope tiles)
- Tap envelope → 800ms reveal animation → modal with grabbed amount
- Live leaderboard (rank, name, ৳)
- Auto-tick: random unclaimed slots fill every 4.5s
- "🧧 WhatsApp-এ পাঠান" share strip at bottom

---

## 9. Orchard 🌳 (`orchard_screen.dart`)

Daily-watering retention loop. Pinduoduo Orchard adaptation.

- Sky-to-grass gradient
- Animated tree (grows visually with `water%`)
- Day counter / streak counter — **"স্ট্রিক ২ দিন পর্যন্ত মিস করলেও ভাঙবে না"** (forgiving — won't break for ≤2 missed days)
- "💧 Water" sticky button → animates drops, increments water + coins
- **Daily tasks** (wires to other features):
  - 💧 দৈনিক পানি — water the tree
  - 🛒 ৳300+ অর্ডার → routes to Cart
  - 👥 বন্ধুকে রেফার → routes to Invite
  - 📺 লাইভ স্ট্রিম দেখুন → routes to Home
  - ✂️ কাটাকাটি খেলুন → routes to Bargain
- Tree picker: mango / lychee / jackfruit
- Friend orchards (cosmetic — Water buttons toast only)
- Harvest button → ship modal → resets day counter, +streak, +coins

---

## 10. Spin & Scratch (`games_screen.dart`)

2 sub-tabs:

### 10.1 Spin
Wheel with 6 segments. Tap "🎰 Spin" → wheel rotates ~3s → result modal ("You won X coins"). Daily-once gating in spec; prototype is unlimited.

### 10.2 Scratch
Card per delivered order. Tap → reveals coin amount. Awards stored in mock CoinStore.

---

## 11. Live shopping (`live_stream_screen.dart`)

Immersive (no bottom nav). Sections:

- Video placeholder (🎥 emoji) + LIVE badge + viewer count
- Host info card (avatar, follow button — toast in prototype)
- **Pinned product card** with current product, price, "Buy" → adds to CartStore + snackbar
- Product strip below — tap to switch active product
- Reactions row (❤️🔥👏🎉) → animated floating reaction emojis
- Chat scroll with auto-incoming fake messages every 1.8s
- Text input + send (appends to local chat list, mock API call)
- "👥 গ্রুপ তৈরি করুন" → routes to group-create

### 11.1 Live checkout (`live_checkout_screen.dart`)
Streamlined cart for in-stream purchase. Mirrors main cart but condensed.

---

## 12. Social tab (`social_screen.dart`)

3 sub-tabs:

- **Nearby**: Map view + list of users in your para (currently hardcoded `_nearby` list — wire to `/social/feed?scope=nearby`)
- **Feed**: Posts from people you follow (groups joined, items bought, products shared)
- **Following**: List of merchants/users you follow

Top: QR scanner toggle (overlay), search toggle (overlay).

---

## 13. Para Neta — Community Leader (`leader_screen.dart`)

3 sub-views: Leaderboard | Leader Dashboard | Become Leader.

### 13.1 Leaderboard view
- Period toggle (weekly / all-time)
- Top 10 list with rank badge, avatar, name, GMV, orders
- "My rank" pinned at bottom

### 13.2 Become Leader view
- Hero gradient with "👑 আপনার পাড়ার নেতা হোন"
- Benefits: ৳8-15k/mo, use existing shop, attract customers, ৳500 sign-up bonus
- Requirements (NID, bKash account, 2hr/day)
- Success story card
- **Trust + accountability card** (critical):
  - 💸 Weekly bKash payouts (Wednesdays, ৳500 minimum)
  - 🚫 No charge for cancelled orders
  - ⚖️ Disputes mediated by ShareDeal (48h response)
  - 📊 Transparent dashboard
- "🚀 আবেদন করুন" CTA

### 13.3 Leader Dashboard
For onboarded leaders. Pickup list, today's orders, commission tracker, payout history.

---

## 14. Profile (`profile_screen.dart`)

Sections top-to-bottom:

### 14.1 Header
- Avatar + name + phone + level badge
- ✏️ Edit profile button → bottom sheet (name / phone / email)
- Stats row: 🪙 coins, 👥 referrals, 💰 savings

### 14.2 Shortcuts grid
🎟️ My Coupons | 📍 Addresses | 💝 Wishlist | 🏆 Achievements

### 14.3 🎮 Games & Rewards card
6-tile grid linking to: 🌳 Orchard | 🎰 Spin & Scratch | 🏆 Leaderboard | ✨ For You | 🧧 Eid Salami | ✂️ Bargain. Discoverability hub for gamification.

### 14.4 Referral card
- Code with copy button
- "Earn ৳50 per friend who orders"
- Friends invited count + Share CTA

### 14.5 Orders + Coins tabs
- **Orders**: Combined OrderStore (newly placed) + SdData seed (history). Each row tappable → tracking screen.
- **Coins**: Ledger of credits/debits.

### 14.6 Low data toggle

### 14.7 Settings list
- 🔔 **Notification settings** → bottom sheet with 5 toggles (order, group, social, live, promo) + Save
- 🔒 **Security** → bottom sheet (Device management, Change PIN, NID verification, Email verification, Logout all sessions)
- 🌐 Change language (toggles BN/EN)
- ❓ Help & Support
- 🚪 Log out (disabled in demo)
- 🧹 Clear demo data

---

## 15. Compare ⚖️

Side-by-side product compare (max 2 SKUs).

- ⚖️ icon on every Explore product card
- Tap once → adds to CompareStore (max 2)
- When 2 selected → snackbar with "View Compare" action → `compare_screen.dart`
- Compare screen shows side-by-side: solo price, group price, savings, group size, rating, sold count, category
- "Clear comparison" button at bottom

---

## 16. Wishlist (`wishlist_screen.dart`)

- List of saved products (from WishlistStore)
- Each row: image, name, price, ❤️ remove
- Tap product → product detail
- "Move to cart" CTA per row

---

## 17. Notifications (`notifications_screen.dart`)

Filter tabs: All | Deals | Social | System.

Tap any notification → routes to its `route` (cart, mygroups, orchard, redenvelope, profile, etc.) + marks read.

Demo notifications include:
- 🔥 Flash sale starting
- ❤️ Price drop on wishlist item
- 🧧 Friend sent Eid Salami
- 🌳 Tree needs water
- 🎉 Referral bonus
- 🛒 **Cart abandoned** ("Your cart is waiting · 1 more friend")
- ⏳ **Group filling fast** ("2 of 3 joined · 19 min left")

"Mark all as read" CTA in top bar.

---

## 18. Merchant storefront (`merchant_store_screen.dart`)

Buyer-facing seller page (different from `merchant_screen.dart` which is the seller's own dashboard).

- Green gradient banner with 🌾 motifs, back arrow, share icon
- Avatar + name + ✓ verified badge + stats line
- Follow / Following toggle button
- Stats row: rating, reviews, orders, years with us
- 3 tabs: **Products** (6-product grid) | **Reviews** | **About** (NID + trade license + delivery time + return policy)

---

## 19. For You feed (`foryou_screen.dart`)

Personalized recommendations:
- Filter chips: All | Trending | Deals | Categories
- Reason cards ("Because you bought rice last week...")
- Product carousels with "based on..." reasoning

---

## 20. Wishlist + Returns + Subscriptions

| Screen | Purpose |
|---|---|
| `wishlist_screen.dart` | Saved items, tap to revisit |
| `returns_*` | List of pending returns + new return form (also reachable from order tracking) |
| `subscriptions_screen.dart` | List active subs, pause/resume/cancel actions |

---

## 21. Group create (`group_create_screen.dart`)

5-step flow:
1. **intro** — pick the product
2. **product** — confirm SKU
3. **settings** — scope (building/para), target size (2/3/4/5/8), duration (12/24/48/72h)
4. **preview** — review the group card
5. **success** — "Group started!" with invite share buttons

---

## 22. Invite (`invite_screen.dart`)

Share modal with:
- Referral code (copy + auto-toast)
- 4 share channels (WhatsApp / Facebook / Messenger / Copy link)
- Contacts list with per-contact "Invite" toggle (state tracked locally)

---

## 23. Landing screens (`landing_screen.dart`)

WhatsApp-acquisition entry points. When a user clicks a shared link they land here:
- **Bargain landing** — preview of the bargain product, "Tap to Cut" CTA → bargain
- **Eid Salami landing** — preview of grabbed amount, "Claim My Share" CTA → eid salami screen

Has a "just browse" fallback link to home.

---

## 24. Shared state stores

All in `lib/v6/state/`. GetX-based reactive — listening widgets rebuild on change.

| Store | Holds | Used by |
|---|---|---|
| `CartStore` | List of `SdCartItem`, total/subtotal/savings/count getters | bottom nav badge, product screen, cart screen, live stream Buy button |
| `WishlistStore` | Set of product IDs | product card hearts, product detail heart, wishlist screen |
| `OrderStore` | List of `SdOrderRow`, `place(...)` returns mock id | profile orders tab, tracking screen |
| `CompareStore` | List of product IDs (max 2) | explore card ⚖️ button, compare screen |

---

## 25. Mock data

`lib/v6/data/sd_data.dart`:
- 3 banners
- 8 categories
- 4 flash deals
- 8 group deals
- 5 live streams
- 15 explore products
- 1 user (Rafi Hasan, ৳240 coins, RAFI240 referral, ধানমন্ডি)
- 6 historical orders (across all status types)
- 2 cart items (initial)

Notification list has 7 demo entries.

Q&A list has 3 entries per product (from `product_screen.dart`).

---

## 26. Production wiring map

Backend already exists at `~/Desktop/sharedeal-social/api` (Laravel 12). Endpoint contract is in MEMORY (`admin_api_endpoints.md` and `sharedeal_social_dev_setup.md`). Quick mapping:

| Prototype source | Real endpoint |
|---|---|
| `SdData.banners` / `categories` / `flashDeals` / `groupDeals` / `liveStreams` | `GET /api/v1/home` |
| `SdData.exploreProducts` | `GET /api/v1/products?category_id&q&sort` |
| Product detail | `GET /api/v1/products/{id}` |
| Open groups list | `GET /api/v1/groups?product_id={id}&scope=para` |
| Join existing group | `POST /api/v1/groups/{id}/join` |
| Create new group | `POST /api/v1/groups` |
| Cart → place order | `POST /api/v1/orders` (with `idempotency_key` UUID) |
| Tracking | `GET /api/v1/orders/{id}/tracking` |
| Bargain tap | `POST /api/v1/bargains/{id}/help` |
| Eid Salami grab | `POST /api/v1/envelopes/{id}/grab` |
| Orchard water | `POST /api/v1/orchard/water` |
| Spin / scratch | `POST /api/v1/games/spin`, `POST /api/v1/games/scratch/{orderId}/reveal` |
| Wishlist toggle | `POST /api/v1/wishlist/{productId}` |
| Profile | `GET /api/v1/profile` |
| Notifications | `GET /api/v1/notifications` |
| Merchant storefront | `GET /api/v1/merchants/{id}` |

API client already scaffolded in `lib/v6/api/*Api` files. Most screens already call the right endpoint with a fallback to `SdData` when API returns nothing — for production, **delete the fallbacks** so real empty state shows instead of fake content.

---

## 27. Cultural / Bangladesh-specific decisions

| Decision | Why |
|---|---|
| **Eid Salami** instead of Red Envelope (Hong Bao) | Cultural fit — Eid gift-giving is the natural local analog |
| **Group scope = building OR para only** | Lane / 2km clutter the picker; 90% of users will pick one of these two |
| **Subscribe gating** to grocery basics | Fish/meat/seasonal items don't recur — hide subscribe CTA |
| **Bargain helpers needed = 3** | Tiny early user base — 8+ friends would never realistically tap |
| **Orchard streak forgiving** (≤2 missed days OK) | Bangladesh users will skip 1 day, lose streak, churn — too punishing |
| **Address requires landmark** | Bangladesh addressing is landmark-driven, not street-numbered |
| **bKash 3-step popup** | Real bKash UX = PIN → spinner → confirmation — must mirror it |
| **Para Neta payouts weekly via bKash** | Trust through transparency (no monthly delays, no deposit required) |
| **Auto-refund language** with timer + ৳ amount | Group buying trust gap — must be explicit at every step |

---

## 28. What's intentionally mocked / TODO for production

These work visually but need real backend wiring:

- **No real auth** — any phone + any 6-digit OTP succeeds. Real version: BulkSMS BD send, 5-min TTL, attempt counter.
- **CartStore not persisted** — refresh empties cart. Real version: server-side cart per user, optimistic local UI.
- **Order placement is local** — no real fulfillment. Production: hit `/orders` endpoint with idempotency key.
- **Bargain tap-to-cut animates locally** — no anti-abuse. Production: server-side rate limiting per device + phone hash.
- **Eid Salami grab is local random** — production needs server-deterministic split.
- **Tracking timeline is mock** — production: rider GPS + server status events.
- **Live stream chat polls a local fake list** — production: WebSocket / FCM realtime.
- **Reviews + Q&A are local** — production: store with verified-purchase flag.
- **Compare doesn't persist** — fine, scoped to session.

---

## 29. Glossary (Bengali ↔ English in app)

| Bengali | English | Where it appears |
|---|---|---|
| গ্রুপ | Group | everywhere |
| জন | people / ppl | tier pricing, group cards |
| সাশ্রয় | Save / savings | tier pricing, cart |
| ফ্ল্যাশ সেল | Flash sale | home, flash screen |
| পাড়া নেতা | Para Neta / Community Leader | leader screen |
| ঈদ সালামি | Eid Salami | post-order, notifications |
| অর্চার্ড | Orchard | gamification |
| কাটাকাটি | Bargain | game |
| ফ্রি জিতুন | Win Free | bargain CTA |
| যত বেশি বন্ধু, তত কম দাম | More friends, lower price | tier pricing header |
| অটো-রিফান্ড | Auto-refund | trust copy |
| ভেরিফাইড | Verified | seller badge |

---

## 30. Build & run

```bash
# Backend (optional — prototype has zero hard dependency on it)
cd ~/Desktop/sharedeal-social/api
php artisan serve --host=0.0.0.0 --port=8000

# Flutter prototype on Android emulator
cd ~/Desktop/sharedeal-social/app
flutter build apk --debug
adb -s emulator-5554 install -r build/app/outputs/flutter-apk/app-debug.apk
adb -s emulator-5554 shell monkey -p com.sharedeal.sharedeal_social -c android.intent.category.LAUNCHER 1

# Or with backend wired:
flutter run -d emulator-5554 \
  --dart-define=SD_BASE_URL=http://10.0.2.2:8000/api/v1 \
  --dart-define=SD_HOST_URL=http://10.0.2.2:8000
```

**OTP for any phone in dev:** `123456` (or any 6 digits in pure prototype mode).

**Demo seed user (when backend is on):** phone `01700000001`, password = OTP `123456`. Has 1 group, 1 delivered order with scratch card, 1 envelope, 1 merchant, 1 live stream, 1 bargain.

---

*End of doc. Each `.dart` file under `lib/v6/screens/` matches one section above. The team should be able to read this doc + open the corresponding screen file + see the matching UX in the running app.*
