# ShareDeal Social — Complete Developer User Stories

> **Project:** Social eCommerce Concept — Bangladesh  
> **Stack:** Flutter (GetX) + Laravel 12 + MySQL  
> **Format:** `As a [role], I want to [goal] so that [benefit].`  
> **Story IDs:** `US-[EPIC]-[NUMBER]`

---

## Epic Index

| Epic | Code | Area |
|---|---|---|
| Onboarding & Auth | AUTH | Registration, OTP, profile setup |
| Home & Discovery | HOME | Banners, categories, flash/group deals |
| Product Detail | PROD | SKU page, pricing modes, seller info |
| Group Buying | GRP | Core group mechanic |
| Cart & Checkout | CART | Cart → address → payment → success |
| Order Tracking | TRK | Status, returns, cancel |
| Bargain | BRG | Win-free price-cutting game |
| Eid Salami | EID | Lucky-envelope gifting |
| Orchard | ORC | Daily-watering retention loop |
| Spin & Scratch | GAME | Micro-games |
| Live Shopping | LIVE | In-stream purchase |
| Social Tab | SOC | Feed, nearby, following |
| Para Neta | PARA | Community leader program |
| Profile | PROF | User hub, settings |
| Compare | CMP | Side-by-side SKU compare |
| Wishlist | WISH | Saved items |
| Notifications | NOTIF | Push & in-app alerts |
| Merchant Storefront | MERCH | Seller public page |
| For You Feed | FYU | Personalized recommendations |
| Subscriptions | SUB | Recurring orders |
| Group Create | GCREAT | Start a new group |
| Invite & Referral | INV | Share + earn |
| i18n & Accessibility | I18N | Language toggle, low-data |
| Backend / API | API | Production wiring |

---

## AUTH — Onboarding & Authentication

### US-AUTH-01
**As a** new user,  
**I want to** see an animated splash screen for 2 seconds when I open the app for the first time,  
**so that** I get a polished first impression of ShareDeal.

**Acceptance Criteria:**
- Green gradient background with 🛒 logo and "ShareDeal" wordmark in yellow
- Tagline "শেয়ার করুন · বাঁচান · জিতুন" is visible
- Animated 3-dot pulse plays during the 2-second wait
- Auto-advances to Slide 1 after 2s (no tap required)
- Only shown on fresh install (first run)

---

### US-AUTH-02
**As a** new user,  
**I want to** swipe through 4 value-proposition slides before registering,  
**so that** I understand why group buying is safe and affordable before I commit my phone number.

**Acceptance Criteria:**
- Slide 1 (green #006A4E): 👥 "একসাথে কিনুন, বেশি বাঁচান!" — group buying
- Slide 2 (red #C62828): ⚡ "প্রতিদিন ফ্ল্যাশ সেল!" — flash deals up to 70% off
- Slide 3 (orange #FF6D00): 🎡 "কেনাকাটা করুন, পুরস্কার জিতুন!" — rewards
- Slide 4 (blue #1565C0): 📍 "কাছের মানুষ, কাছের দাম!" — local groups
- Yellow indicator dots (active = wide, others = narrow)
- Yellow CTA: "পরবর্তী →" on slides 1–3, "শুরু করুন 🚀" on slide 4
- "এড়িয়ে যান" (Skip) link visible on slides 1–3
- Swiping left/right navigates between slides
- Skip jumps directly to Phone screen

---

### US-AUTH-03
**As a** new user,  
**I want to** enter my Bangladesh phone number to register,  
**so that** my identity is tied to a real phone number for trust and OTP delivery.

**Acceptance Criteria:**
- Green gradient header
- 🇧🇩 +880 country prefix is fixed and pre-filled
- Input accepts exactly 11 digits (01XXXXXXXXX format)
- "Continue" / CTA button is disabled until 11 digits entered
- Social login buttons (Facebook / Google) displayed below as alternatives
- Invalid length shows inline error before submission

---

### US-AUTH-04
**As a** new user,  
**I want to** receive and enter a 6-digit OTP to verify my phone number,  
**so that** only real owners of the phone can register.

**Acceptance Criteria:**
- 6 individual digit boxes displayed
- Resend countdown timer shown (60s)
- "Resend" link enabled after timer expires
- Maximum 5 OTP attempts before lockout
- OTP expires after 5 minutes
- In production: BulkSMS BD used for delivery
- Error shown if wrong code entered
- Auto-advances to Profile setup on success

---

### US-AUTH-05
**As a** new user,  
**I want to** set up my profile (photo, name, area) after OTP verification,  
**so that** ShareDeal can personalize my home feed and seed me into the right para.

**Acceptance Criteria:**
- Photo placeholder tappable (opens image picker)
- Name input (required)
- Area/neighborhood input (required)
- Welcome bonus card shown: "৳50 cashback + 50 coins"
- "ShareDeal শুরু করুন 🚀" CTA submits and routes to V6Shell home
- `Pref.write(KeyWord.onboardingDone, true)` saved so onboarding is skipped on all subsequent launches

---

### US-AUTH-06
**As a** returning user,  
**I want to** be taken directly to the home screen when I reopen the app,  
**so that** I don't have to go through onboarding again.

**Acceptance Criteria:**
- `onboardingDone` flag is checked on launch
- If flag = true, splash auto-routes to V6Shell (home tab)
- Onboarding slides are never shown again

---

### US-AUTH-07
**As a** user,  
**I want to** log out of my account,  
**so that** I can switch accounts or protect my data on a shared device.

**Acceptance Criteria:**
- Logout option in Profile → Settings
- In production: clears GetStorage token + redirects to phone screen
- In demo: disabled with a tooltip ("Disabled in demo")

---

### US-AUTH-08
**As a** user,  
**I want to** manage active device sessions and change my PIN,  
**so that** I can secure my account if I suspect unauthorized access.

**Acceptance Criteria:**
- Security bottom sheet accessible from Profile → Settings → Security
- Shows: Device management, Change PIN, NID verification, Email verification, Logout all sessions
- Each action has its own sub-flow or placeholder

---

---

## HOME — Home & Discovery

### US-HOME-01
**As a** user,  
**I want to** see a persistent bottom navigation bar with 5 tabs (Home, Explore, Social, Cart, Profile),  
**so that** I can switch between the main sections of the app at any time.

**Acceptance Criteria:**
- Tabs: 🏠 Home | 🔍 Explore | 📡 Social | 🛒 Cart | 👤 Profile
- Active tab is visually highlighted
- Switching tabs preserves scroll position
- Cart tab shows a live badge with current item count
- Nav bar stays visible on all main screens (hidden only on immersive screens: live stream, bargain, eid salami)

---

### US-HOME-02
**As a** user,  
**I want to** see a search pill at the top of the home screen,  
**so that** I can quickly jump to the Explore tab to search for products.

**Acceptance Criteria:**
- Search pill is tappable (does not open inline search — routes to Explore tab)
- Lang toggle and 🔔 Notifications icon also in top bar
- Notification icon shows red dot when unread notifications exist

---

### US-HOME-03
**As a** user,  
**I want to** see an auto-rotating banner carousel at the top of the home screen,  
**so that** I can discover active campaigns and deals.

**Acceptance Criteria:**
- 3 banners rotate automatically every 3 seconds
- Dot indicators show current position; tapping dots jumps to that banner
- Each banner has a "এখনই দেখুন →" CTA
- CTA routes to the correct destination (flash / profile / explore depending on banner)
- Auto-rotation pauses if user manually swipes

---

### US-HOME-04
**As a** user,  
**I want to** browse category chips horizontally below the banners,  
**so that** I can quickly filter products by type.

**Acceptance Criteria:**
- 8 chips: চাল-ডাল, সবজি, মাছ-মাংস, তেল-মশলা, দুগ্ধজাত, স্ন্যাকস, ক্লিনিং, আরও
- Chips scroll horizontally
- Tapping a chip routes to Explore tab pre-filtered with that category
- "আরও" chip opens full category list

---

### US-HOME-05
**As a** user,  
**I want to** see a flash deals carousel with countdown timers on the home screen,  
**so that** I don't miss time-limited discounts.

**Acceptance Criteria:**
- Section header "⚡ ফ্ল্যাশ সেল" with overall countdown and "সব দেখুন →" link
- 4 product cards horizontally scrollable
- Each card shows: image, name, dealPrice (red), originalPrice (strikethrough), discount %, sold-progress bar, countdown
- Tapping a card → product detail in flash mode
- "সব দেখুন →" → Explore filtered to flash deals

---

### US-HOME-06
**As a** user,  
**I want to** see active group deals on the home screen with a "Join & Share" button,  
**so that** I can quickly join a group buying opportunity without searching.

**Acceptance Criteria:**
- Section header "👥 গ্রুপ ডিল" with "সব দেখুন →" link
- Cards show: image, name, group price, progress (X/Y joined), time remaining
- "Join & Share" button opens the invite flow for that group
- Tapping the card body → product detail in group mode

---

### US-HOME-07
**As a** user,  
**I want to** see a strip of live streams on the home screen,  
**so that** I can tap in and watch live product demos.

**Acceptance Criteria:**
- 3–5 live cards with seller avatar, product thumbnail, LIVE badge, viewer count
- Tapping a card → live stream viewer screen
- Cards refresh on scroll-to-refresh

---

### US-HOME-08
**As a** user,  
**I want to** see gamification feature CTAs (Para Neta, Orchard, Games, Bargain, For You) on the home screen,  
**so that** I can discover and re-enter reward features easily.

**Acceptance Criteria:**
- Para Neta card → leader screen
- For You card → for-you feed
- Orchard card → orchard screen
- Games card → spin & scratch screen
- Bargain card → bargain screen
- Cards are visually distinct and labeled in Bengali + emoji

---

---

## PROD — Product Detail

### US-PROD-01
**As a** user,  
**I want to** see a full product detail page when I tap any product,  
**so that** I can review all information before deciding to buy.

**Acceptance Criteria:**
- Hero image (square aspect) with discount % badge and ❤️/🤍 wishlist toggle
- Back arrow, lang toggle, cart icon (with badge) in top bar
- Tapping ❤️ toggles wishlist state and shows a snackbar confirmation

---

### US-PROD-02
**As a** user,  
**I want to** toggle between Solo and Group pricing modes on the product page,  
**so that** I can compare the individual price vs. the group-buy price and choose accordingly.

**Acceptance Criteria:**
- 2-tab toggle at top of price section: Solo | Group
- Default = Group for group/flash products; Solo for browse-origin products
- Price block updates instantly on tab switch
- Group mode shows: solo price (struck), group price (large green), discount %, savings, group filling progress + avatar pile, countdown timer

---

### US-PROD-03
**As a** user,  
**I want to** see tiered group pricing (3/5/10 people) on the product page,  
**so that** I understand that inviting more friends saves everyone more money.

**Acceptance Criteria:**
- Header: "যত বেশি বন্ধু, তত কম দাম"
- Tile 1 (white bg, green border): "৩ জন", price, savings
- Tile 2 (cream bg, dashed orange border): "৫ জন", price, savings
- Tile 3 (red bg, yellow border, 🔥 "সেরা" floating chip): "১০ জন", price, savings
- Active tier highlighted based on currently selected/joined group size

---

### US-PROD-04
**As a** user browsing grocery staples,  
**I want to** see a Subscribe & Save option on eligible products,  
**so that** I can set up automatic reorders and save ~10% on items I buy regularly.

**Acceptance Criteria:**
- Subscribe section visible ONLY for: rice, oil, milk, eggs, dal, ghee, sugar, salt, biscuits, water
- Hidden for fish, meat, seasonal, and non-grocery items
- Cadence picker: weekly / biweekly / monthly
- Subscribe price shown (~10% off solo price)
- Tapping subscribe → confirmation bottom sheet

---

### US-PROD-05
**As a** user,  
**I want to** see open groups for a product filtered by my building or neighborhood,  
**so that** I can join a group with people I know or who are nearby.

**Acceptance Criteria:**
- Section header "৩টি গ্রুপ চলছে — যোগ দিন!" (count is dynamic)
- Scope picker: 🏢 আমার বিল্ডিং | 🏘️ আমার পাড়া
- Each row: leader avatar, name, locality tag, joined count, countdown, "যোগ দিন" button
- Tapping "যোগ দিন" selects that group (sets `_selectedGroupId`) and changes bottom CTA from "নতুন গ্রুপ" to "যোগ দিন"

---

### US-PROD-06
**As a** user,  
**I want to** see verified seller information on the product page,  
**so that** I can trust the merchant before buying.

**Acceptance Criteria:**
- Green-bordered card: seller name, ✓ verified badge, NID-checked, dispute count
- Escrow chip: "আপনার টাকা সুরক্ষিত (Escrow) — ৭ দিনের ফেরত গ্যারান্টি"
- Tapping card → merchant storefront screen
- Delivery row: 🚚 দ্রুত ডেলিভারি, ↩️ রিটার্ন, ✅ গ্যারান্টি chips

---

### US-PROD-07
**As a** user,  
**I want to** read product descriptions, reviews, and Q&A on the product page,  
**so that** I can make an informed purchase decision.

**Acceptance Criteria:**
- 3-tab section: বিবরণ | রিভিউ | প্রশ্ন
- Reviews tab: list with star rating, reviewer name, date, body
- Q&A tab: list of questions + answers; "Ask a question" form posts inline
- Submitted question appears immediately in the list (optimistic UI)

---

### US-PROD-08
**As a** user in Solo mode,  
**I want to** see 3 action buttons (Switch to Group, Add to Cart, Buy Now) at the bottom of the product page,  
**so that** I can quickly take the action that fits my intent.

**Acceptance Criteria:**
- Button 1 (light green): "👥 গ্রুপে -৳N" — switches to Group mode
- Button 2 (orange): "🛒 কার্টে যোগ" — adds to CartStore + snackbar with "View Cart"
- Button 3 (red): "⚡ এখনই কিনুন · ৳N" — adds + navigates to Cart
- Buttons are sticky (visible above keyboard and on scroll)

---

### US-PROD-09
**As a** user in Group mode,  
**I want to** see 3 action buttons (Win Free, Q&A, Join Group) at the bottom,  
**so that** I can choose to bargain for free, ask a question, or commit to the group.

**Acceptance Criteria:**
- Button 1 (yellow gradient): "✂️ ফ্রি জিতুন" → Bargain screen for this product
- Button 2 (light green): "💬 প্রশ্ন" → switches to Q&A tab
- Button 3 (green): "🛒 যোগ দিন · ৳N" → if group selected: join existing; else: push group-create flow

---

---

## GRP — Group Buying Core Flow

### US-GRP-01
**As a** user,  
**I want to** join an existing open group for a product,  
**so that** I can get the group price without waiting to recruit friends myself.

**Acceptance Criteria:**
- User selects a group from the open-groups list on the product page
- Tapping "যোগ দিন · ৳N" adds item to CartStore with `isGroup=true` and the selected group ID
- Cart success screen shows "Joined Group" state
- Escrow chip visible: "গ্রুপ পূর্ণ না হলে অটো-রিফান্ড" with timer + amount

---

### US-GRP-02
**As a** user,  
**I want to** start a new buying group when no suitable open group exists,  
**so that** I can recruit friends and still get the group discount.

**Acceptance Criteria:**
- If no group selected on product page, "যোগ দিন" routes to Group Create wizard
- After creation, cart success screen shows "Group Started!" state
- Group appears in Profile → My Groups → Active tab

---

### US-GRP-03
**As a** user in an active group,  
**I want to** track the group's filling status in real time,  
**so that** I know how many more people need to join before the order confirms.

**Acceptance Criteria:**
- Profile → My Groups → Active tab shows all active groups
- Each group card: product name, current/target count, countdown, "Invite" CTA
- Notification sent when group fills: "🎉 Group filled! Order confirmed"

---

### US-GRP-04
**As a** user,  
**I want to** be automatically refunded if my group doesn't fill within 24 hours,  
**so that** I have zero financial risk when joining a group.

**Acceptance Criteria:**
- Refund copy visible at every step: cart success, notifications, order detail
- Format: "৳[amount] ওয়ালেটে ফেরত যাবে [time remaining] এর মধ্যে"
- On expiry: mock refund credited to wallet + push notification
- Refund reflects in Profile → Coins/Wallet ledger

---

### US-GRP-05
**As a** user whose group has filled,  
**I want to** see my order move through the fulfillment pipeline,  
**so that** I know my delivery is on track.

**Acceptance Criteria:**
- Status timeline: placed → packed → rider picked up → out for delivery → delivered
- Push notification at each stage change
- Scratch card unlocked on delivery notification

---

---

## CART — Cart & Checkout

### US-CART-01
**As a** user,  
**I want to** review all items in my cart before checking out,  
**so that** I can adjust quantities or remove items before paying.

**Acceptance Criteria:**
- Item rows: image, name, isGroup tag (if applicable), qty +/- buttons, line total, ✕ remove
- Removing last item shows empty-cart state with "Start Shopping" CTA
- Cart count badge in bottom nav updates live

---

### US-CART-02
**As a** user,  
**I want to** apply a coupon code to my cart,  
**so that** I can redeem discounts I've earned or received.

**Acceptance Criteria:**
- Coupon input + "Apply" button in cart step
- In production: validate against `/api/v1/coupons/validate`
- In prototype: accepts any code, applies ৳30 off
- Applied discount shown in price summary as separate line item
- Invalid code shows inline error

---

### US-CART-03
**As a** user,  
**I want to** see a full price breakdown before placing my order,  
**so that** there are no surprises at payment.

**Acceptance Criteria:**
- Price summary shows: Subtotal, Deal savings (green), Coupon discount (green), Delivery fee, **Total** (bold)
- Group savings clearly labeled separately from coupon savings

---

### US-CART-04
**As a** user,  
**I want to** select or add a delivery address,  
**so that** my order is delivered to the right location.

**Acceptance Criteria:**
- List of saved addresses displayed
- "+ নতুন ঠিকানা" expands an add-address form
- Form fields: Flat# + Road/Lane (side-by-side), **Landmark (required)**, Area/Neighborhood
- Faux map preview with 📍 pin (Bangladesh landmark-driven addressing)
- "Save" composes fields into a single line and persists locally (GET Storage; production: POST /api/v1/addresses)
- Selected address is highlighted

---

### US-CART-05
**As a** user,  
**I want to** choose Cash on Delivery as my payment method,  
**so that** I can pay when the order arrives without a digital wallet.

**Acceptance Criteria:**
- COD is default selected payment radio
- COD-specific toggle: "Confirm with a phone call before delivery"
- Selecting COD + Place Order → 700ms simulated processing → success screen

---

### US-CART-06
**As a** user,  
**I want to** pay with bKash through a familiar 3-step popup,  
**so that** the in-app payment experience matches what I already know from bKash.

**Acceptance Criteria:**
- bKash radio selectable in payment step
- On "Place Order": popup opens with 3 stages:
  1. PIN entry (5 digits masked) + amount + Cancel button
  2. "Verifying..." spinner (~1.4s delay)
  3. ✓ Confirmed with mock TrxID (format: BKS-XXXXXX)
- Cancel → returns to payment step without placing order
- On confirm → success screen, order appended to OrderStore, cart cleared

---

### US-CART-07
**As a** user,  
**I want to** see a success screen after placing my order,  
**so that** I have confirmation and next steps.

**Acceptance Criteria:**
- ✅ Large checkmark + "অর্ডার সফল হয়েছে!"
- Label: "Group Started!" / "Joined the Group!" / standard for solo
- Order number and ETA displayed
- Escrow chip visible
- Group auto-refund promise card (yellow) if applicable
- bKash verification card if paid via bKash
- Eid Salami share card with "Send to friends" CTA
- "Track Order" CTA → tracking screen for new order
- "পরে দেখব · হোমে ফিরুন" link

---

---

## TRK — Order Tracking, Returns & Cancel

### US-TRK-01
**As a** user,  
**I want to** track my order on a live map with a rider position,  
**so that** I know exactly where my delivery is.

**Acceptance Criteria:**
- Map preview with rider position animating from origin to destination
- Rider card: name, phone CTA (tap to call)
- ETA displayed
- Full status timeline: placed → packed → rider picked up → out for delivery → delivered (active stage highlighted with timestamp)
- Order summary at bottom: ID, item, price

---

### US-TRK-02
**As a** user who has received a delivered order,  
**I want to** request a return or refund within 7 days,  
**so that** I'm protected if the item is damaged, wrong, or not as described.

**Acceptance Criteria:**
- "↩️ রিটার্ন/রিফান্ড চান?" yellow card visible only when status = delivered
- Opens return bottom sheet with:
  - Reason radio: damaged / wrong item / quality / not as described / other
  - Description textarea
  - "Add photos" placeholder button
  - Submit → snackbar "✅ Return sent · response within 24h"
- Seller response SLA = 24h

---

### US-TRK-03
**As a** user who placed an order recently,  
**I want to** cancel it within 30 minutes of placing,  
**so that** I can correct a mistake before it's processed.

**Acceptance Criteria:**
- "🛑 Cancel order" red card visible ONLY if status = placed/processing/packed AND order is < 30 min old
- Opens cancel bottom sheet with reason radio: changed mind / found cheaper / delivery slow / wrong address / other
- On confirm: snackbar "✅ Cancelled · refund to wallet in 48h" + navigates back
- Cancelled order status updates in order list

---

---

## BRG — Bargain (Win Free)

### US-BRG-01
**As a** user,  
**I want to** enter the Bargain game for a product,  
**so that** I can potentially win it for free by getting 3 friends to help cut the price.

**Acceptance Criteria:**
- Bargain screen accessible from: Product detail "✂️ ফ্রি জিতুন" CTA, Home gamification card, Profile Games & Rewards
- Hero card shows: large current price (animated cut-amount pop on each reduction), countdown timer
- Progress bar showing % toward FREE
- Helpers list (top 6) with avatars and ৳ contributed

---

### US-BRG-02
**As a** user on the Bargain screen,  
**I want to** tap a button to simulate a friend helping cut the price,  
**so that** I can see the mechanic in action during the demo.

**Acceptance Criteria:**
- "✂️ Tap to help cut" button auto-decrements price and adds a helper entry
- Price updates with animation
- Share strip: WhatsApp / Facebook / SMS / Copy → toast + auto-simulates a friend-help (demo aliveness)
- Reward ladder shown: 50% off → 75% off → FREE!

---

### US-BRG-03
**As a** user,  
**I want to** win the product for free when 3 helpers have participated,  
**so that** I receive the reward for sharing with friends.

**Acceptance Criteria:**
- When progress ≥ 100%: "🎉 Won FREE!" modal appears
- "Claim" button routes to Cart with item in group-join mode at ৳0
- `_friendsNeeded` tunable via a single getter (default = 3)

---

### US-BRG-04
**As a** friend who tapped a shared Bargain link,  
**I want to** land on a branded Bargain landing page,  
**so that** I understand the offer before being prompted to install the app.

**Acceptance Criteria:**
- Landing page shows product preview + current price + "Tap to Cut" CTA
- CTA deep-links into app bargain screen (or app store if not installed)
- "Just browse" fallback link to home

---

---

## EID — Eid Salami (Lucky Envelope)

### US-EID-01
**As a** user who just placed an order,  
**I want to** send an Eid Salami pool to friends via WhatsApp,  
**so that** they can grab a share and I can feel generous.

**Acceptance Criteria:**
- Post-purchase success screen shows "🧧 Send Eid Salami on WhatsApp" card
- Tapping opens Eid Salami screen
- Hero pool meter shows total ৳ with 8 envelope tiles
- "🧧 WhatsApp-এ পাঠান" share button at bottom

---

### US-EID-02
**As a** friend who received an Eid Salami link,  
**I want to** tap an envelope to grab a random amount,  
**so that** I can claim my share of the gift.

**Acceptance Criteria:**
- 800ms reveal animation on tap
- Modal shows grabbed amount
- Live leaderboard updates: rank, name, ৳
- Auto-tick: random unclaimed slots fill every 4.5s (demo liveliness)
- Claimed amounts credited to recipient's wallet (mock)

---

### US-EID-03
**As a** friend clicking a shared Eid Salami link,  
**I want to** see a branded landing page showing what I can claim,  
**so that** I'm motivated to install the app and collect my share.

**Acceptance Criteria:**
- Landing page shows friend's name + claimable amount
- "Claim My Share" CTA deep-links to app Eid Salami screen
- "Just browse" fallback to home

---

---

## ORC — Orchard

### US-ORC-01
**As a** user,  
**I want to** water my virtual fruit tree daily,  
**so that** I can earn coins and eventually receive a real box of fruit after 14 days.

**Acceptance Criteria:**
- Screen: sky-to-grass gradient, animated tree that grows with water%
- Day counter and streak counter visible
- "💧 Water" sticky button → water-drop animation, increments water + coins
- Water streak: ≤2 missed days do NOT break the streak (forgiving mechanic)
- Day 14: "Harvest" button appears

---

### US-ORC-02
**As a** user,  
**I want to** complete daily tasks in the Orchard to earn extra water and coins,  
**so that** I have a reason to engage with other parts of the app every day.

**Acceptance Criteria:**
- Daily tasks panel with 5 tasks:
  - 💧 দৈনিক পানি — water the tree
  - 🛒 ৳300+ অর্ডার — routes to Cart
  - 👥 বন্ধুকে রেফার — routes to Invite
  - 📺 লাইভ স্ট্রিম দেখুন — routes to Home (live section)
  - ✂️ কাটাকাটি খেলুন — routes to Bargain
- Completed tasks show a checkmark and cannot be re-completed today

---

### US-ORC-03
**As a** user,  
**I want to** choose my tree type (mango / lychee / jackfruit),  
**so that** I receive a fruit variety I actually want.

**Acceptance Criteria:**
- Tree picker displayed on Orchard screen
- Selection persists in GetStorage
- Visual tree sprite changes to reflect selection

---

### US-ORC-04
**As a** user,  
**I want to** see my friends' orchards,  
**so that** I can feel a sense of community around the feature.

**Acceptance Criteria:**
- Friend orchards section (cosmetic in prototype)
- "Water" buttons for friend trees show a toast ("Coming soon!" or "Watered!")
- Production: actual cross-water mechanic via `/api/v1/orchard/water-friend`

---

### US-ORC-05
**As a** user who has reached day 14,  
**I want to** harvest my tree and schedule a real fruit delivery,  
**so that** I receive the physical reward I've been growing.

**Acceptance Criteria:**
- "Harvest 🌳" button appears on day 14+
- Tapping opens ship modal: confirms delivery address
- On confirm: day counter resets, streak +1, coins credited, snackbar "✅ Fruit order placed!"
- Production: `POST /api/v1/orchard/harvest` triggers fulfillment

---

---

## GAME — Spin & Scratch

### US-GAME-01
**As a** user,  
**I want to** spin a reward wheel once per day,  
**so that** I have a reason to open the app even on days I'm not shopping.

**Acceptance Criteria:**
- Wheel with 6 segments (coin amounts: 5, 10, 15, 20, 30, 50)
- "🎰 Spin" button → wheel rotates ~3s → lands on segment → result modal
- Modal shows won amount with "Collect" CTA
- Daily-once gating in production (prototype: unlimited)
- Coins added to CoinStore

---

### US-GAME-02
**As a** user with a delivered order,  
**I want to** scratch a card to reveal a coin reward,  
**so that** I get a surprise bonus at the moment my delivery arrives.

**Acceptance Criteria:**
- Scratch tab shows one unscratched card per delivered order
- Tap → scratch reveal animation → coin amount shown
- Reward stored in CoinStore
- Already-scratched cards shown as revealed (greyed out)

---

---

## LIVE — Live Shopping

### US-LIVE-01
**As a** user,  
**I want to** watch a live shopping stream,  
**so that** I can see a product demonstrated in real time before buying.

**Acceptance Criteria:**
- Video placeholder + LIVE badge + live viewer count
- Immersive layout (bottom nav hidden)
- Host info card: avatar, name, Follow button (toast in prototype)
- Pinned product card: current product + price + "Buy" CTA

---

### US-LIVE-02
**As a** user watching a live stream,  
**I want to** buy the pinned product without leaving the stream,  
**so that** I can purchase at the moment of peak interest without losing context.

**Acceptance Criteria:**
- "Buy" on pinned product card → adds to CartStore + snackbar
- Product strip below: tap to switch which product is active/pinned
- Condensed live-checkout screen available for full checkout without exiting stream

---

### US-LIVE-03
**As a** user watching a live stream,  
**I want to** react and chat in real time,  
**so that** I feel part of a live community event.

**Acceptance Criteria:**
- Reactions row: ❤️🔥👏🎉 → tapping sends animated floating emoji
- Chat scroll with auto-incoming fake messages every 1.8s (prototype)
- Text input + Send → appends message to local chat list
- Production: WebSocket / FCM for real-time chat

---

### US-LIVE-04
**As a** user in a live stream,  
**I want to** start a group buy from within the stream,  
**so that** I can ride the shared excitement of the stream to recruit group members.

**Acceptance Criteria:**
- "👥 গ্রুপ তৈরি করুন" button in live stream screen
- Routes to Group Create wizard pre-filled with the currently pinned product

---

---

## SOC — Social Tab

### US-SOC-01
**As a** user,  
**I want to** see a map and list of nearby users in my para,  
**so that** I know who I could buy with and trust their buying decisions.

**Acceptance Criteria:**
- "Nearby" sub-tab with map view + user list
- Users shown with avatar, name, distance
- Production: wired to `GET /social/feed?scope=nearby`

---

### US-SOC-02
**As a** user,  
**I want to** see a social feed of what people I follow are buying and sharing,  
**so that** I can discover products through trusted recommendations.

**Acceptance Criteria:**
- "Feed" sub-tab shows posts: groups joined, items bought, products shared
- Each post: avatar, action description, product thumbnail, time
- Tapping product → product detail

---

### US-SOC-03
**As a** user,  
**I want to** follow merchants and other users,  
**so that** their activity appears in my feed.

**Acceptance Criteria:**
- "Following" sub-tab shows list of followed merchants/users
- Follow/Unfollow toggle on each item
- Follow state synced across: social screen, merchant storefront, live stream host card

---

### US-SOC-04
**As a** user,  
**I want to** scan another user's QR code to connect with them,  
**so that** I can quickly add someone I meet in person to my network.

**Acceptance Criteria:**
- QR scanner toggle in social tab top bar
- Opens camera overlay
- Scanning a valid ShareDeal QR shows user profile + Follow CTA

---

---

## PARA — Para Neta (Community Leader)

### US-PARA-01
**As a** user,  
**I want to** see the weekly and all-time leaderboard of Para Netas,  
**so that** I can see who the top community leaders are in my area.

**Acceptance Criteria:**
- Period toggle: Weekly | All-time
- Top 10 rows: rank badge, avatar, name, GMV (৳), order count
- "My rank" pinned at bottom even if outside top 10

---

### US-PARA-02
**As a** user,  
**I want to** apply to become a Para Neta,  
**so that** I can earn commission running a local pickup point out of my shop.

**Acceptance Criteria:**
- "Become Leader" view with hero gradient "👑 আপনার পাড়ার নেতা হোন"
- Benefits listed: ৳8-15k/mo, use existing shop, attract customers, ৳500 sign-up bonus
- Requirements listed: NID, bKash account, 2hr/day
- Success story card for social proof
- Trust & accountability card:
  - 💸 Weekly bKash payouts (Wednesdays, ৳500 minimum)
  - 🚫 No charge for cancelled orders
  - ⚖️ ShareDeal mediates disputes (48h SLA)
  - 📊 Transparent dashboard
- "🚀 আবেদন করুন" CTA submits application (mock POST → snackbar)

---

### US-PARA-03
**As an** onboarded Para Neta,  
**I want to** see my leader dashboard with today's pickups and earnings,  
**so that** I can manage my local pickup point efficiently.

**Acceptance Criteria:**
- Today's orders list with status
- Pickup list (orders ready for collection)
- Commission tracker (daily/weekly ৳)
- Payout history (date, amount, status)
- Dashboard only visible to users with leader role

---

---

## PROF — Profile

### US-PROF-01
**As a** user,  
**I want to** view and edit my profile information,  
**so that** my name, photo, and contact details are up to date.

**Acceptance Criteria:**
- Header: avatar + name + phone + level badge
- Stats row: 🪙 coins, 👥 referrals, 💰 total savings
- ✏️ Edit button opens bottom sheet: name / phone / email fields
- Save updates GetStorage (production: PATCH /api/v1/profile)

---

### US-PROF-02
**As a** user,  
**I want to** access quick shortcut tiles for Coupons, Addresses, Wishlist, and Achievements,  
**so that** I can reach these features without navigating through multiple menus.

**Acceptance Criteria:**
- Shortcuts grid: 🎟️ My Coupons | 📍 Addresses | 💝 Wishlist | 🏆 Achievements
- Each tile navigates to the correct screen

---

### US-PROF-03
**As a** user,  
**I want to** see all gamification features in one Games & Rewards card,  
**so that** I don't miss reward opportunities scattered across the app.

**Acceptance Criteria:**
- 6-tile grid: 🌳 Orchard | 🎰 Spin & Scratch | 🏆 Leaderboard | ✨ For You | 🧧 Eid Salami | ✂️ Bargain
- Each tile routes to the respective screen

---

### US-PROF-04
**As a** user,  
**I want to** see my full order history,  
**so that** I can track past purchases, reorder, or raise issues.

**Acceptance Criteria:**
- Orders tab in Profile: combined OrderStore (new) + SdData seed (history)
- Each row: order thumbnail, name, date, status badge, total
- Tapping a row → tracking screen for that order
- All status types represented: processing, packed, out for delivery, delivered, cancelled

---

### US-PROF-05
**As a** user,  
**I want to** see my coin ledger,  
**so that** I know how many coins I have and how I earned or spent them.

**Acceptance Criteria:**
- Coins tab: chronological list of credit (+) and debit (-) entries
- Each entry: action label, amount (green/red), date
- Total balance shown at top
- Production: wired to `GET /api/v1/profile/coins`

---

### US-PROF-06
**As a** user,  
**I want to** manage notification preferences,  
**so that** I only receive alerts I care about.

**Acceptance Criteria:**
- Profile → Settings → Notification settings
- 5 toggles: Order updates | Group updates | Social | Live streams | Promotions
- Save button persists preferences

---

### US-PROF-07
**As a** user on a slow connection,  
**I want to** enable Low Data Mode,  
**so that** the app uses less bandwidth and loads faster.

**Acceptance Criteria:**
- Low data toggle in Profile
- When enabled: images load at lower resolution, animations are reduced
- Preference persists in GetStorage

---

---

## CMP — Product Compare

### US-CMP-01
**As a** user browsing products,  
**I want to** add up to 2 products to a compare list,  
**so that** I can see them side-by-side before deciding which to buy.

**Acceptance Criteria:**
- ⚖️ icon on every Explore product card
- First tap adds to CompareStore; second tap on different product adds it
- Adding a third product replaces the older one with a warning toast
- When exactly 2 products in CompareStore: snackbar "View Compare" action appears

---

### US-CMP-02
**As a** user,  
**I want to** view a side-by-side comparison of two products,  
**so that** I can make a data-driven buying decision.

**Acceptance Criteria:**
- Compare screen shows both products in two columns
- Rows: Image, Name, Solo price, Group price, Savings ৳, Group size needed, Rating ⭐, Sold count, Category
- Visual winner highlight (e.g. lower price column highlighted in green)
- "Clear comparison" button at bottom resets CompareStore

---

---

## WISH — Wishlist

### US-WISH-01
**As a** user,  
**I want to** save products to my wishlist by tapping the heart icon,  
**so that** I can find them again later without searching.

**Acceptance Criteria:**
- ❤️/🤍 toggle on product cards and product detail page
- Tap → snackbar "Added to wishlist" or "Removed from wishlist"
- Heart state is shared: same product ID reflected consistently across all cards, product detail, and wishlist screen

---

### US-WISH-02
**As a** user,  
**I want to** view all my saved products in one list,  
**so that** I can review my saved items and decide what to buy.

**Acceptance Criteria:**
- Wishlist screen: list of items from WishlistStore
- Each row: image, name, group price, ❤️ remove button
- Tapping product row → product detail
- "Move to cart" per row → adds to CartStore + snackbar

---

---

## NOTIF — Notifications

### US-NOTIF-01
**As a** user,  
**I want to** see all my notifications in one place with filter tabs,  
**so that** I can quickly find the alerts that matter to me.

**Acceptance Criteria:**
- Filter tabs: All | Deals | Social | System
- Each notification: icon, title, body, timestamp, unread dot
- Unread dot on 🔔 icon in home top bar when unread notifications exist
- "Mark all as read" CTA clears all unread dots

---

### US-NOTIF-02
**As a** user,  
**I want to** tap a notification to navigate directly to the relevant screen,  
**so that** I can act on alerts with one tap.

**Acceptance Criteria:**
- 🔥 Flash sale → explore (flash filtered)
- ❤️ Price drop → product detail for that item
- 🧧 Eid Salami received → eid salami screen
- 🌳 Tree needs water → orchard
- 🎉 Referral bonus → profile coins
- 🛒 Cart abandoned → cart screen
- ⏳ Group filling fast → tracking / my groups
- Tapping marks that notification as read

---

### US-NOTIF-03
**As a** user,  
**I want to** receive a push notification when my cart is abandoned,  
**so that** I'm reminded to complete my purchase.

**Acceptance Criteria:**
- Notification: "Your cart is waiting · 1 more friend" (or similar)
- Deep links to cart screen
- Sent after X minutes of cart inactivity (production: configurable, e.g. 30 min)

---

### US-NOTIF-04
**As a** user in an active group,  
**I want to** receive an urgent notification when my group is nearly full,  
**so that** I can invite the last member before the deadline.

**Acceptance Criteria:**
- Notification: "⏳ 2 of 3 joined · 19 min left"
- Deep links to My Groups → active group
- Sent when group reaches 1 member short of target

---

---

## MERCH — Merchant Storefront

### US-MERCH-01
**As a** user,  
**I want to** view a merchant's public storefront page,  
**so that** I can see all their products and trust signals before buying repeatedly from them.

**Acceptance Criteria:**
- Green gradient banner with 🌾 motifs + back arrow + share icon
- Avatar + name + ✓ verified badge + stats line (rating, reviews, orders, years)
- Follow / Following toggle (state synced with social following)
- 3 tabs: Products (6-product grid) | Reviews | About

---

### US-MERCH-02
**As a** user,  
**I want to** see verified credentials (NID, trade license) and policies on the merchant's About tab,  
**so that** I can assess the seller's legitimacy and understand returns.

**Acceptance Criteria:**
- About tab shows: NID verification status, trade license number, delivery time range, return policy text
- Dispute count visible
- Data sourced from `GET /api/v1/merchants/{id}`

---

---

## FYU — For You Feed

### US-FYU-01
**As a** user,  
**I want to** see personalized product recommendations with a "because you..." reason,  
**so that** I discover relevant products without having to search.

**Acceptance Criteria:**
- Filter chips: All | Trending | Deals | Categories
- Reason cards: e.g. "Because you bought rice last week..."
- Product carousels per reason category
- Tapping product → product detail

---

---

## SUB — Subscriptions

### US-SUB-01
**As a** user with an active subscription,  
**I want to** view and manage all my recurring orders,  
**so that** I can pause, resume, or cancel them as needed.

**Acceptance Criteria:**
- Subscriptions screen: list of active subscriptions
- Each row: product image, name, cadence, next delivery date, price
- Actions: Pause | Resume | Cancel
- Pause/resume updates next delivery date
- Cancel with confirmation prompt

---

---

## GCREAT — Group Create

### US-GCREAT-01
**As a** user,  
**I want to** create a new buying group through a 5-step wizard,  
**so that** the process is broken into easy choices and I end on a shareable link.

**Acceptance Criteria:**
- Step 1 (intro): pick the product
- Step 2 (product): confirm the selected SKU
- Step 3 (settings): scope (building/para), target size (2/3/4/5/8), duration (12/24/48/72h)
- Step 4 (preview): review the group card
- Step 5 (success): "Group started!" + invite share buttons (WhatsApp / Facebook / Copy)
- Back button navigates to previous step
- Progress indicator shows current step

---

---

## INV — Invite & Referral

### US-INV-01
**As a** user,  
**I want to** share my referral code with friends and earn ৳50 per friend who orders,  
**so that** I'm rewarded for growing the platform.

**Acceptance Criteria:**
- Referral code displayed in Profile referral card with copy button
- Copy → toast "Copied!"
- Friends invited count shown
- Share CTA opens invite screen

---

### US-INV-02
**As a** user,  
**I want to** invite friends via WhatsApp, Facebook, Messenger, or a copied link,  
**so that** I can reach them on the channels they actually use.

**Acceptance Criteria:**
- Invite screen: referral code + copy, 4 share channels (WhatsApp / Facebook / Messenger / Copy link)
- Contacts list with per-contact "Invite" toggle (state tracked locally)
- WhatsApp is channel #1 (first in layout)
- Share action generates pre-filled message with referral link

---

---

## I18N — Internationalisation & Accessibility

### US-I18N-01
**As a** user,  
**I want to** toggle between Bengali and English at any point in the app,  
**so that** I can use the app in my preferred language.

**Acceptance Criteria:**
- `SdLangToggle` widget in every screen's top bar
- Toggle persists in GetStorage
- All user-visible strings go through `t(bn, en)` from `sd_lang.dart`
- App re-renders current screen in new language immediately on toggle (no full restart)

---

### US-I18N-02
**As a** developer,  
**I want to** add new UI strings via a single `t(bn, en)` helper,  
**so that** there is one canonical place for all bilingual strings.

**Acceptance Criteria:**
- No hardcoded Bengali or English strings in widget files (all routed through `t()`)
- `sd_lang.dart` is the sole string registry
- Missing string key logs a warning in debug mode

---

---

## API — Production Wiring

### US-API-01
**As a** developer,  
**I want to** delete all mock data fallbacks from API client files,  
**so that** real empty states are shown in production instead of fake content.

**Acceptance Criteria:**
- All `*Api` files under `lib/v6/api/` call real endpoints
- On empty API response: empty-state widget shown (no SdData fallback)
- List of endpoints mapped from spec Section 26

---

### US-API-02
**As a** developer,  
**I want to** wire `POST /api/v1/orders` with an idempotency key,  
**so that** duplicate order placement (e.g. double-tap) is prevented.

**Acceptance Criteria:**
- Client generates a UUID `idempotency_key` per cart checkout session
- Key sent as header `Idempotency-Key: <uuid>`
- Server rejects duplicate key within 5 minutes (returns 200 with original response)

---

### US-API-03
**As a** developer,  
**I want to** replace the mock OTP with BulkSMS BD integration,  
**so that** real phone verification is enforced in production.

**Acceptance Criteria:**
- OTP sent via BulkSMS BD API on phone number submission
- 6-digit code, 5-minute TTL, 5-attempt lockout
- Correct OTP verified server-side (not client-side)
- Dev seed: phone `01700000001` + OTP `123456` still works in staging

---

### US-API-04
**As a** developer,  
**I want to** persist CartStore server-side per user session,  
**so that** a user's cart survives app refresh or reinstall.

**Acceptance Criteria:**
- Cart operations POST/PATCH/DELETE to `/api/v1/cart`
- On app open (authenticated): `GET /api/v1/cart` hydrates CartStore
- Local CartStore is optimistic; conflicts resolved server-side on next sync
- Cart badge count updates immediately on local add/remove

---

### US-API-05
**As a** developer,  
**I want to** replace local tracking mock with real rider GPS and server status events,  
**so that** order tracking reflects live delivery state.

**Acceptance Criteria:**
- `GET /api/v1/orders/{id}/tracking` returns current status + rider lat/lng
- App polls this endpoint every 30s while tracking screen is open (or uses WebSocket push)
- Map pin moves smoothly to new coordinates on update
- Timeline highlights active stage with real server timestamp

---

### US-API-06
**As a** developer,  
**I want to** add server-side rate limiting to the Bargain tap endpoint,  
**so that** the Bargain mechanic cannot be gamed by bots or fast-tappers.

**Acceptance Criteria:**
- `POST /api/v1/bargains/{id}/help` accepts at most 1 request per device per minute
- Excess requests return 429 with retry-after header
- Client shows "Please wait..." toast on 429

---

### US-API-07
**As a** developer,  
**I want to** implement server-deterministic Eid Salami splits,  
**so that** the total claimed never exceeds the pool and every user gets a fair random share.

**Acceptance Criteria:**
- `POST /api/v1/envelopes/{id}/grab` returns server-computed amount
- Sum of all grabs ≤ pool total
- If all 8 envelopes claimed, endpoint returns 410 Gone
- Client shows correct grabbed amount from server response (not locally computed)

---

### US-API-08
**As a** developer,  
**I want to** replace live-stream fake chat with WebSocket or FCM real-time messages,  
**so that** live stream chat works between real users.

**Acceptance Criteria:**
- WebSocket connection established on live stream screen open
- Incoming messages appended to chat scroll in real time
- Outgoing messages sent over WebSocket + optimistic local append
- Connection gracefully closed when user leaves stream
- Fallback to long-polling if WebSocket fails

---

### US-API-09
**As a** developer,  
**I want to** add the verified-purchase flag to reviews and Q&A,  
**so that** buyers can trust that feedback is from real purchasers.

**Acceptance Criteria:**
- Review API returns `is_verified_purchase: boolean`
- Verified reviews show a "✓ Verified Purchase" badge
- Q&A: user can only post after a confirmed order for that product
- Unverified Q&A still visible but labeled differently

---

---

## Summary Counts

| Epic | Stories |
|---|---|
| AUTH | 8 |
| HOME | 8 |
| PROD | 9 |
| GRP | 5 |
| CART | 7 |
| TRK | 3 |
| BRG | 4 |
| EID | 3 |
| ORC | 5 |
| GAME | 2 |
| LIVE | 4 |
| SOC | 4 |
| PARA | 3 |
| PROF | 7 |
| CMP | 2 |
| WISH | 2 |
| NOTIF | 4 |
| MERCH | 2 |
| FYU | 1 |
| SUB | 1 |
| GCREAT | 1 |
| INV | 2 |
| I18N | 2 |
| API | 9 |
| **TOTAL** | **98** |

---

*Generated from the canonical UX spec at [social-ecommerce-concept-in-bangladesh](https://github.com/arafatomer66/social-ecommerce-concept-in-bangladesh). Each story maps to a screen file in `lib/v6/screens/`.*
