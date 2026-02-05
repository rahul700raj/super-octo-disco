# Bloom - Mobile Application Specification 📱

**App Type:** Gamified Habit Tracker & Wellness App  
**Target Users:** Gen Z Students & Early Professionals (Age 18-30)  
**Monetization:** Freemium (Ads for free users / ₹199/mo Subscription for Premium)  
**Technology Preference:** Flutter (Frontend) + Firebase (Backend)

---

## 1. App Idea Explanation (Elevator Pitch)
**Problem:** Aajkal students aur young professionals ke paas motivation ki kami hai. Wo routine start toh karte hain par bore hokar chhod dete hain. Traditional to-do lists boring lagti hain aur koi instant reward nahi milta.

**Solution:** **Bloom** ek "Digital Garden" hai jahan aapki habits aapke plants hain. Jab aap paani peete hain ya padhai karte hain, aap app mein ek button tap karte hain aur aapka virtual plant grow karta hai. Agar aap habit skip karte hain, toh plant murjhaane lagta hai. Ye emotional connection aur visual reward system users ko consistent rehne mein help karta hai.

**USP:** Sirf tick-mark lagana nahi, balki apni progress ko ek growing garden ke roop mein dekhna. "Grow yourself to grow your garden."

---

## 2. Feature List

### MVP (Minimum Viable Product) - Launch Day 1
*   **Quick Habit Add:** "+" button dabakar 3 seconds mein nayi habit (e.g., "Drink Water") add karein.
*   **Gamified Check-in:** Task complete karne par "Watering Animation" play hoga (max 2 seconds latency).
*   **Streak Counter:** Home screen par current streak dikhegi (e.g., "🔥 5 Days").
*   **Basic Push Reminders:** Subah 8 baje aur Shaam 8 baje notification aayega (Delivery rate > 95%).
*   **Focus Timer:** 25-minute Pomodoro timer with simple start/stop functionality.

### Phase-2 (Post Launch Update)
*   **Social Garden:** Doston ke garden visit karna aur unke plants ko water karna (Real-time sync).
*   **Plant Shop:** Earned coins se naye pots aur rare seeds khareedna.
*   **Advanced Analytics:** Weekly graphs jo dikhayein ki kis din productivity sabse high thi.
*   **Widget Support:** Home screen widget se direct habit tick karna without opening app.
*   **Cloud Backup:** Google Drive sync taaki device change karne par data lost na ho.

---

## 3. User Flow (Step by Step)

1.  **Install & Open:** User app kholta hai → Splash Screen with blooming animation → User feels **"Excited & Welcomed"**.
2.  **Onboarding:** "Set your first goal" screen aati hai → User "Drink Water" select karta hai → User feels **"It's easy"**.
3.  **Sign Up (Optional for MVP):** "Skip for now" button dabata hai → Direct Home Screen par land karta hai → User feels **"Relieved (No friction)"**.
4.  **First Action:** User "Drink Water" card par tap karta hai → Paani dalne ka sound aur animation play hota hai → Plant thoda bada hota hai → User feels **"Satisfied/Rewarded"**.
5.  **Closing:** User app band karta hai lekin notification permission "Allow" karta hai taaki plant na mare → User feels **"Responsible"**.

---

## 4. Screen-wise UI Layout

### A. Splash Screen
*   **Above-the-fold:** Animated Bloom Logo (Center).
*   **Primary CTA:** Loading indicator (Self-transition).
*   **Accessibility:** Screen reader says "Bloom app loading, please wait."

### B. Home Screen (The Garden)
*   **Above-the-fold:**
    *   Top Left: Greeting "Hi, [Name]" & Current Level (Lvl 5).
    *   Center: 3D-style Plant Image (State dependent: Healthy/Withered).
*   **Content:** List of Habits (Cards) below the plant.
*   **Primary CTA:** Floating Action Button (FAB) "+" Icon (Color: #4CAF50 - Green).
*   **Bottom-Nav Icons:** Garden (Active), Focus, Profile.
*   **Empty State:** Agar koi habit nahi hai, text: "Plant a seed to start growing."
*   **Accessibility:** "Double tap to water your plant for [Habit Name]."

### C. Add Habit Screen
*   **Above-the-fold:** Input field "Name your habit" (Auto-focus).
*   **Content:** Frequency selector (Daily/Weekly) chips.
*   **Primary CTA:** "Plant Seed" Button (Bottom sticky, Width: Full).
*   **Accessibility:** Input field labeled "Enter habit name, for example, Read Book."

### D. Focus Mode Screen
*   **Above-the-fold:** Big Circular Timer (25:00).
*   **Primary CTA:** "Start Focus" (Color: #2196F3 - Blue).
*   **Bottom-Nav:** Hidden during focus session to reduce distraction.
*   **Empty State:** N/A.
*   **Accessibility:** Timer reads out remaining time every minute if TalkBack enabled.

---

## 5. Database Structure (Simple JSON-like)

**Database:** Firestore (NoSQL)

```json
// 1. Users Collection
{
  "uid": "user_123",
  "name": "Rahul",
  "email": "rahul@example.com",
  "total_coins": 150,
  "created_at": "timestamp",
  "premium_status": false
  // Index: premium_status (for analytics)
}

// 2. Habits Collection
{
  "habit_id": "h_001",
  "user_id": "user_123", // Index: user_id (Must have)
  "title": "Drink Water",
  "frequency": ["Mon", "Tue", "Wed", "Thu", "Fri", "Sat", "Sun"],
  "reminder_time": "08:00 AM",
  "current_streak": 5,
  "is_archived": false
}

// 3. DailyLogs Collection (History)
{
  "log_id": "l_999",
  "habit_id": "h_001", // Index: habit_id + date
  "date": "2023-10-27",
  "status": "COMPLETED",
  "timestamp": "2023-10-27T08:05:00Z"
}

// 4. PlantState Collection
{
  "user_id": "user_123",
  "plant_type": "Sunflower",
  "growth_stage": 3, // 1=Seed, 5=Fully Bloomed
  "health": 100 // 0-100%
}
```

---

## 6. Basic Backend Logic

| Endpoint | Method | Route | Body | One-line Business Rule |
| :--- | :--- | :--- | :--- | :--- |
| **Create Habit** | POST | `/api/habits` | `{title, frequency, time}` | User can create max 3 habits in Free plan, unlimited in Premium. |
| **Complete Habit** | POST | `/api/habits/{id}/check` | `{date, local_time}` | Log entry create karo aur User ke coins +10 increase karo immediately. |
| **Get Home Data** | GET | `/api/home` | `None` (Use Auth Token) | Return active habits list AND current plant state in single response (latency < 200ms). |
| **Sync Progress** | POST | `/api/sync` | `{offline_logs: []}` | Offline logs ko validate karo (future dates not allowed) aur DB update karo. |

---

## 7. Future Upgrade Ideas

1.  **AI Habit Coach (Tech: OpenAI API / Gemini API):**
    *   **Idea:** AI user ke past data ko analyze karke personalized suggestions dega. "Rahul, tum weekends pe workout skip karte ho, Saturday ko reminder time change karein?"
2.  **Offline Resume Scoring (Tech: TensorFlow Lite):**
    *   **Idea:** Agar ye Job app hota toh resume scan karte, but habit context mein: **"Pose Detection for Yoga"**. Camera se user ka posture check karna bina internet ke.
3.  **Wear OS / Watch App (Tech: Flutter Wear):**
    *   **Idea:** Habit reminders direct smart watch pe bhejna aur wahi se "Done" mark karna.
