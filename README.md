# 📘 ExamNotesAI

An AI-powered exam notes generator that converts any topic into structured, exam-focused study material — complete with diagrams, charts, revision points, important questions, and PDF export.

Built with the **MERN stack** + **Google Gemini API** + **Firebase Auth** + **Stripe Payments**.

---

## 🚀 Features

| Feature | Description |
|---|---|
| 🤖 AI Note Generation | Powered by Google Gemini — generates structured, exam-ready notes for any topic |
| 📂 Sub-Topic Breakdown | Classifies content into ⭐ / ⭐⭐ / ⭐⭐⭐ priority tiers |
| ⚡ Revision Mode | Generates ultra-short bullet-point cheat sheets for last-minute prep |
| 📊 Mermaid Diagrams | Auto-generates flowcharts rendered with Mermaid.js |
| 📈 Visual Charts | Bar, line, and pie charts via Recharts for data-heavy topics |
| ❓ Important Questions | Short answer, long answer, and diagram-based exam questions |
| ⬇️ PDF Download | Exports full notes as a clean, printable PDF using PDFKit |
| 📚 Notes History | Browse and re-read all previously generated notes |
| 💳 Credit System | Pay-per-use model — each generation costs 10 credits |
| 💰 Stripe Payments | Buy credit packs (₹100 / ₹200 / ₹500) via Stripe Checkout |
| 🔐 Google Auth | One-click sign-in via Firebase Google Authentication |

---

## 🛠️ Tech Stack

### Frontend (client/)
| Technology | Purpose |
|---|---|
| React 19 + Vite 7 | Core UI framework |
| Tailwind CSS 4 | Styling |
| Redux Toolkit | Global state (user data, credits) |
| React Router DOM 7 | Client-side routing |
| Axios | HTTP requests to backend |
| Firebase | Google OAuth authentication |
| Mermaid.js | Flowchart / diagram rendering |
| Recharts | Bar, line, pie chart rendering |
| React Markdown | Markdown-to-HTML rendering for notes |
| Motion (Framer Motion) | Animations and transitions |

### Backend (server/)
| Technology | Purpose |
|---|---|
| Node.js + Express 5 | REST API server |
| MongoDB + Mongoose | Database for users and notes |
| Google Gemini API | AI note generation (`gemini-3-flash-preview`) |
| PDFKit | PDF generation and streaming |
| Stripe | Payment processing and webhooks |
| JWT | Session authentication via HTTP-only cookies |
| dotenv | Environment variable management |

---

## 📁 Project Structure

```
ExamNotesAI/
├── client/                         # React frontend (Vite)
│   ├── public/
│   │   └── logo.png
│   ├── src/
│   │   ├── assets/                 # Static images
│   │   ├── components/
│   │   │   ├── FinalResult.jsx     # Renders all AI output sections
│   │   │   ├── MermaidSetup.jsx    # Mermaid diagram renderer
│   │   │   ├── RechartSetUp.jsx    # Chart renderer
│   │   │   ├── Sidebar.jsx         # Notes sidebar panel
│   │   │   ├── TopicForm.jsx       # Input form with toggles
│   │   │   ├── Navbar.jsx
│   │   │   └── Footer.jsx
│   │   ├── pages/
│   │   │   ├── Home.jsx            # Landing page
│   │   │   ├── Auth.jsx            # Google sign-in page
│   │   │   ├── Notes.jsx           # Main notes generation page
│   │   │   ├── History.jsx         # Past notes list
│   │   │   ├── Pricing.jsx         # Credit purchase page
│   │   │   ├── PaymentSuccess.jsx
│   │   │   └── PaymentFailed.jsx
│   │   ├── redux/
│   │   │   ├── store.js
│   │   │   └── userSlice.js        # User data & credits in Redux
│   │   ├── services/
│   │   │   └── api.js              # Axios API calls
│   │   ├── utils/
│   │   │   └── firebase.js         # Firebase init + Google provider
│   │   ├── App.jsx                 # Routes + auth guards
│   │   └── main.jsx
│   └── package.json
│
└── server/                         # Express backend
    ├── controllers/
    │   ├── auth.controller.js      # Google sign-in / logout
    │   ├── generate.controller.js  # AI notes generation
    │   ├── notes.controller.js     # Fetch saved notes
    │   ├── pdf.controller.js       # PDF generation
    │   ├── credits.controller.js   # Stripe checkout + webhook
    │   └── user.controller.js      # Get current user
    ├── middleware/
    │   └── isAuth.js               # JWT cookie verification
    ├── models/
    │   ├── user.model.js           # User schema (name, email, credits, notes[])
    │   └── notes.model.js          # Notes schema (topic, content, flags)
    ├── routes/
    │   ├── auth.route.js
    │   ├── genrate.route.js
    │   ├── notes.route.js (via genrate.route.js)
    │   ├── pdf.route.js
    │   ├── credits.route.js
    │   └── user.route.js
    ├── services/
    │   └── gemini.services.js      # Gemini API call + JSON parsing
    ├── utils/
    │   ├── connectDb.js            # MongoDB connection
    │   ├── promptBuilder.js        # Builds the Gemini prompt
    │   └── token.js                # JWT sign/verify
    ├── index.js                    # Express app entry point
    └── package.json
```

---

## ⚙️ How It Works

### Note Generation Flow

1. User fills out the **TopicForm** — enters topic, class level, exam type, and toggles (Revision Mode / Diagram / Charts).
2. Frontend calls `POST /api/notes/generate-notes` with the payload.
3. Backend checks user credits (minimum 10 required).
4. `promptBuilder.js` constructs a strict JSON-format prompt for Gemini.
5. Gemini returns a structured JSON object with:
   - `subTopics` — priority-classified topic list
   - `importance` — overall star rating
   - `notes` — Markdown-formatted detailed notes
   - `revisionPoints` — bullet-point cheat sheet
   - `questions` — short, long, and diagram questions
   - `diagram` — Mermaid flowchart string (if requested)
   - `charts` — Recharts-compatible data arrays (if requested)
6. Notes are saved to MongoDB and 10 credits are deducted.
7. Frontend renders everything in `FinalResult.jsx`.

### Auth Flow

1. User clicks "Sign in with Google" on the Auth page.
2. Firebase handles the OAuth popup and returns a Google user object.
3. Frontend sends `{ name, email }` to `POST /api/auth/google`.
4. Backend creates or fetches the user in MongoDB.
5. A JWT is signed and set as an **HTTP-only cookie** (7-day expiry).
6. Subsequent requests use the cookie; `isAuth` middleware verifies it.

### Payment Flow

1. User selects a credit plan on the Pricing page.
2. Frontend calls `POST /api/credit/order` with the amount.
3. Backend creates a **Stripe Checkout Session** and returns the URL.
4. User is redirected to Stripe's hosted payment page.
5. On success, Stripe sends a webhook to `POST /api/credits/webhook`.
6. Backend verifies the webhook signature and adds credits to the user's account via `$inc`.

---

## 🔑 Environment Variables

### Server (`server/.env`)

```env
PORT=8000
MONGODB_URI=your_mongodb_atlas_connection_string
JWT_SECRET=your_jwt_secret_key
GEMINI_API_KEY=your_google_gemini_api_key
STRIPE_SECRET_KEY=your_stripe_secret_key
STRIPE_WEBHOOK_SECRET=your_stripe_webhook_signing_secret
CLIENT_URL=http://localhost:5173
```

### Client (`client/.env`)

```env
VITE_FIREBASE_APIKEY=your_firebase_api_key
```

---

## 🧑‍💻 Getting Started

### Prerequisites

- Node.js v18+
- MongoDB Atlas account (or local MongoDB)
- Google Gemini API key
- Firebase project with Google Auth enabled
- Stripe account

### 1. Clone the Repository

```bash
git clone https://github.com/Aditya123-bit/ExamNotesAI.git
cd ExamNotesAI
```

### 2. Setup the Server

```bash
cd server
npm install
```

Create a `.env` file in the `server/` directory and add all the environment variables listed above.

```bash
npm run dev
# Server starts on http://localhost:8000
```

### 3. Setup the Client

```bash
cd ../client
npm install
```

Create a `.env` file in the `client/` directory and add the Firebase API key.

```bash
npm run dev
# Frontend starts on http://localhost:5173
```

---

## 📡 API Reference

### Auth

| Method | Endpoint | Description | Auth |
|---|---|---|---|
| POST | `/api/auth/google` | Sign in / register via Google | No |
| POST | `/api/auth/logout` | Clear JWT cookie | No |

### User

| Method | Endpoint | Description | Auth |
|---|---|---|---|
| GET | `/api/user/currentuser` | Get logged-in user data | ✅ |

### Notes

| Method | Endpoint | Description | Auth |
|---|---|---|---|
| POST | `/api/notes/generate-notes` | Generate AI notes (costs 10 credits) | ✅ |
| GET | `/api/notes/my-notes` | Get all saved notes (metadata only) | ✅ |
| GET | `/api/notes/:id` | Get full content of a single note | ✅ |

### PDF

| Method | Endpoint | Description | Auth |
|---|---|---|---|
| POST | `/api/pdf/generate-pdf` | Stream a PDF of the notes | ✅ |

### Credits

| Method | Endpoint | Description | Auth |
|---|---|---|---|
| POST | `/api/credit/order` | Create Stripe checkout session | ✅ |
| POST | `/api/credits/webhook` | Stripe webhook — adds credits on payment | No (raw body) |

---

## 💳 Credit Plans

| Plan | Price (INR) | Credits | Cost per Note |
|---|---|---|---|
| Starter | ₹100 | 50 credits | ~₹20/note |
| Popular | ₹200 | 120 credits | ~₹16.67/note |
| Pro Learner | ₹500 | 300 credits | ~₹16.67/note |

> New users receive **50 free credits** on signup.  
> Each note generation costs **10 credits**.

---

## 🗃️ Database Schema

### User

```js
{
  name: String,          // required
  email: String,         // unique, required
  credits: Number,       // default: 50
  isCreditAvailable: Boolean, // default: true
  notes: [ObjectId],     // refs to Notes documents
  timestamps: true
}
```

### Notes

```js
{
  user: ObjectId,        // ref to UserModel
  topic: String,         // required
  classLevel: String,
  examType: String,
  revisionMode: Boolean, // default: false
  includeDiagram: Boolean,
  includeChart: Boolean,
  content: Mixed,        // full Gemini JSON response
  timestamps: true
}
```

---

## 🤖 Gemini Prompt Structure

The `promptBuilder.js` sends a carefully structured prompt that forces Gemini to return strict, parseable JSON. Key rules enforced in the prompt:

- All output must be valid JSON (parsed with `JSON.parse()`)
- Notes field must use Markdown formatting
- `diagram.data` must be valid Mermaid `graph TD` syntax with labels in `[ ]`
- Charts must use Recharts-compatible `{ name, value }` format
- Sub-topics are classified into three star tiers based on exam frequency
- Revision Mode forces ultra-short, one-line bullet outputs

---

## 🔐 Security Notes

- JWT is stored in an **HTTP-only cookie** — not accessible via JavaScript
- Stripe webhook events are verified using the **signing secret** before processing
- The `/api/credits/webhook` route uses `express.raw()` (required for Stripe signature validation)
- All protected routes pass through the `isAuth` middleware

---

## 🌐 Deployment Notes

- Update `serverUrl` in `client/src/App.jsx` to your deployed backend URL before building
- Update the CORS `origin` in `server/index.js` to your frontend's production domain
- Add your production domain to Stripe's webhook endpoint settings
- Set `secure: true` and `sameSite: 'none'` on the JWT cookie (already set) for cross-origin deployments

---

## 📄 License

ISC License

---

## 👨‍💻 Author

**Aditya Yadav**  
B.Tech CSE Student, SISTec GN, Bhopal  
GitHub: [@Aditya123-bit](https://github.com/Aditya123-bit)
