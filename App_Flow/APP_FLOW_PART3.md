# 📋 Complete Application Workflow Guide - SkillProof (Part 3 - Final)

*Continuing from Part 2...*

---

## ⚠️ Error Handling Workflows

### 1. **API Error Handling Flow**

```
START
  ↓
API call initiated
  ↓
Network request sent
  ↓
Response received
  ↓
CHECK: HTTP Status Code
  ├─ 200-299 (SUCCESS) → Process response → END
  │
  ├─ 400 (BAD REQUEST)
  │   ├─ Validation error
  │   ├─ Show user-friendly error message
  │   └─ Highlight invalid fields
  │
  ├─ 401 (UNAUTHORIZED)
  │   ├─ Token expired/invalid
  │   ├─ Try refresh token (if available)
  │   ├─ If refresh fails → Clear tokens → Redirect to login
  │   └─ Show: "Session expired. Please login again."
  │
  ├─ 403 (FORBIDDEN)
  │   ├─ Insufficient permissions
  │   ├─ Show: "You don't have permission to access this."
  │   └─ Log security event
  │
  ├─ 404 (NOT FOUND)
  │   ├─ Resource not found
  │   ├─ Show: "Resource not found."
  │   └─ Redirect to appropriate page
  │
  ├─ 429 (RATE LIMIT)
  │   ├─ Too many requests
  │   ├─ Show: "Too many requests. Please try again later."
  │   └─ Display retry-after time
  │
  ├─ 500-599 (SERVER ERROR)
  │   ├─ Server error occurred
  │   ├─ Log error to Sentry/error tracking
  │   ├─ Show: "Something went wrong. Please try again."
  │   └─ Offer "Retry" button
  │
  └─ NETWORK ERROR
      ├─ No internet connection
      ├─ Request timeout
      └─ Connection refused
      ↓
      Show: "Network error. Please check your connection."
      ├─ Enable offline mode (if applicable)
      └─ Queue request for retry
  ↓
END
```

### 2. **Blockchain Transaction Error Handling Flow**

```
START
  ↓
User initiates blockchain transaction (e.g., NFT minting)
  ↓
Transaction prepared
  ↓
User approves transaction in wallet
  ↓
Transaction sent to blockchain
  ↓
Monitor transaction status
  ↓
CHECK: Transaction Status
  ├─ PENDING → Show "Transaction pending..." → Wait
  │
  ├─ CONFIRMED → Success → Continue normal flow
  │
  ├─ FAILED → Handle failure
  │   ├─ Reasons:
  │   │   ├─ Insufficient gas
  │   │   ├─ Reverted (contract error)
  │   │   ├─ Nonce too low
  │   │   └─ Network congestion
  │   ├─ Show error message:
  │   │   ├─ "Transaction failed. Please try again."
  │   │   └─ Specific error if available
  │   ├─ Offer solutions:
  │   │   ├─ "Increase gas limit"
  │   │   ├─ "Retry transaction"
  │   │   └─ "Contact support"
  │   └─ Log error for debugging
  │
  └─ TIMEOUT → Handle timeout
      ├─ Transaction stuck (low gas)
      ├─ Show: "Transaction is taking longer than expected"
      ├─ Options:
      │   ├─ Wait longer
      │   ├─ Cancel and retry with higher gas
      │   └─ Check on block explorer
      └─ Monitor in background
  ↓
END
```

### 3. **Interview Error Handling Flow**

```
START
  ↓
Candidate taking interview
  ↓
Error occurs during interview
  ↓
CHECK: Error Type
  ├─ CODE EXECUTION ERROR
  │   ├─ Sandbox timeout
  │   ├─ Memory limit exceeded
  │   ├─ Syntax error (handled gracefully)
  │   └─ Show: "Code execution error. Please check your code."
  │
  ├─ AI SERVICE ERROR
  │   ├─ Question generation failed
  │   ├─ Evaluation service down
  │   ├─ Show: "Service temporarily unavailable. Retrying..."
  │   ├─ Retry with exponential backoff
  │   └─ Fallback to cached questions (if available)
  │
  ├─ NETWORK ERROR
  │   ├─ Connection lost during interview
  │   ├─ Show: "Connection lost. Reconnecting..."
  │   ├─ Save interview state locally
  │   ├─ Auto-save code periodically
  │   └─ Resume when connection restored
  │
  ├─ SESSION EXPIRED
  │   ├─ Interview session timeout
  │   ├─ Show: "Session expired. Your progress has been saved."
  │   └─ Offer: "Resume Interview" or "Start New"
  │
  └─ BROWSER/TAB CLOSED
      ├─ Detect page unload
      ├─ Warn: "Are you sure? Progress may be lost."
      ├─ Save state before closing
      └─ Allow resume on return
  ↓
Error logged
  ├─ Sent to error tracking service
  └─ Include: session ID, error type, timestamp
  ↓
END
```

### 4. **Authentication Error Flow**

```
START
  ↓
User tries to access protected resource
  ↓
API call made with token
  ↓
API returns 401 Unauthorized
  ↓
Catch 401 error
  ↓
CHECK: Refresh token available?
  ├─ YES → Try refresh token
  │   ├─ API: POST /api/auth/refresh
  │   ├─ SUCCESS → Update token → Retry original request
  │   └─ FAIL → Continue to logout
  │
  └─ NO → Continue to logout
  ↓
Clear stored tokens
  ├─ Remove from localStorage
  └─ Clear from Redux state
  ↓
Update Redux: logout()
  ├─ Clear user data
  ├─ Set isAuthenticated: false
  └─ Clear all user-related state
  ↓
Redirect to login page
  ↓
Show message: "Session expired. Please login again."
  ↓
Optional: Store return URL for redirect after login
  ↓
END
```

---

## 🎯 Complete User Journeys

### 1. **First-Time Candidate Journey (End-to-End)**

```
STEP 1: DISCOVERY
  ↓
User visits skillproof.io
  ↓
Views landing page
  ├─ Sees value proposition
  ├─ Understands AI + Blockchain verification
  └─ Clicks "Get Verified"
  ↓

STEP 2: SIGNUP
  ↓
Navigates to /signup?role=candidate
  ↓
Chooses registration method:
  ├─ Email/Password
  │   ├─ Fills registration form
  │   ├─ Verifies email
  │   └─ Account created
  │
  └─ Wallet (Web3)
      ├─ Connects MetaMask
      ├─ Signs message
      └─ Account created
  ↓
Redirected to /dashboard/candidate
  ↓

STEP 3: PROFILE SETUP
  ↓
Profile completion wizard
  ├─ Step 1: Basic info (name, location, bio)
  ├─ Step 2: Skills selection
  ├─ Step 3: Resume upload (AI parsing)
  └─ Step 4: GitHub integration (optional)
  ↓
Profile saved
  ↓

STEP 4: SKILL VERIFICATION
  ↓
Selects skill to verify (e.g., "React")
  ↓
Reviews interview instructions
  ↓
Starts AI interview
  ├─ Question generated
  ├─ Writes code in editor
  ├─ Tests solution
  └─ Submits final code
  ↓
AI evaluates submission
  ├─ Score calculated: 78/100
  ├─ Report generated
  └─ Eligible for NFT (score >= 70)
  ↓

STEP 5: NFT MINTING
  ↓
Views interview results
  ↓
Clicks "Mint SkillProof NFT"
  ↓
Connects wallet (if not connected)
  ↓
Approves minting transaction
  ↓
NFT minted on Polygon
  ├─ Token ID received
  ├─ Transaction confirmed
  └─ Badge appears on profile
  ↓

STEP 6: SHARE PROFILE
  ↓
Enables public profile
  ↓
Gets shareable link
  ↓
Shares on LinkedIn/Twitter
  ↓

END JOURNEY
```

### 2. **Returning Candidate Journey**

```
STEP 1: LOGIN
  ↓
User visits skillproof.io
  ↓
Clicks "Login"
  ↓
Enters credentials OR connects wallet
  ↓
Redirected to dashboard
  ↓

STEP 2: DASHBOARD OVERVIEW
  ↓
Views dashboard
  ├─ Sees verified skills (3 skills verified)
  ├─ Average score: 82/100
  ├─ Recent activity
  └─ "Verify New Skill" CTA
  ↓

STEP 3: VERIFY ADDITIONAL SKILL
  ↓
Selects new skill (e.g., "Node.js")
  ↓
Takes interview (familiar with process)
  ↓
Completes successfully
  ↓
Mints NFT
  ↓

STEP 4: PROFILE MANAGEMENT
  ↓
Updates profile
  ├─ Adds new project to portfolio
  ├─ Updates GitHub integration
  └─ Refreshes resume
  ↓

END JOURNEY
```

### 3. **First-Time Employer Journey**

```
STEP 1: DISCOVERY
  ↓
User visits skillproof.io
  ↓
Views landing page
  ↓
Clicks "Find Talent"
  ↓

STEP 2: SIGNUP
  ↓
Navigates to /signup?role=employer
  ↓
Fills employer registration
  ├─ Company name
  ├─ Business email
  ├─ Password
  └─ Terms acceptance
  ↓
Account created
  ↓

STEP 3: SUBSCRIPTION SELECTION
  ↓
Views pricing plans
  ↓
Selects "Growth" plan ($299/month)
  ├─ 100 candidate views/month
  ├─ Advanced search
  └─ Candidate comparison
  ↓
Completes payment
  ↓
Subscription activated
  ↓

STEP 4: DASHBOARD EXPLORATION
  ↓
Views employer dashboard
  ├─ Quick stats
  ├─ Search interface
  └─ Tutorial/onboarding
  ↓

STEP 5: CANDIDATE SEARCH
  ↓
Searches for candidates
  ├─ Skills: ["React", "TypeScript"]
  ├─ Min score: 75
  └─ Location: Remote
  ↓
Views search results (20 candidates)
  ↓

STEP 6: CANDIDATE EVALUATION
  ↓
Clicks on candidate profile
  ↓
Views detailed profile
  ├─ Verified skills with scores
  ├─ SkillProof NFTs
  ├─ GitHub stats
  └─ Verification dates
  ↓
Clicks "Verify on Blockchain"
  ├─ On-chain verification successful
  └─ ✅ Credential confirmed authentic
  ↓
Views interview report
  ├─ Full AI evaluation
  ├─ Code submission
  └─ Detailed feedback
  ↓
Saves candidate
  ↓

STEP 7: HIRING DECISION
  ↓
Compares multiple candidates
  ↓
Makes hiring decision
  ↓
Contacts candidate (via platform or external)
  ↓

END JOURNEY
```

### 4. **Returning Employer Journey**

```
STEP 1: LOGIN
  ↓
User logs in
  ↓
Redirected to dashboard
  ↓

STEP 2: SAVED SEARCH CHECK
  ↓
Views saved searches
  ↓
Sees new candidate matches notification
  ↓
Clicks to view new matches
  ↓

STEP 3: CANDIDATE REVIEW
  ↓
Reviews new candidates
  ↓
Filters and sorts results
  ↓
Opens interesting profiles
  ↓
Verifies credentials on-chain
  ↓

STEP 4: COMPARISON
  ↓
Selects top 3 candidates
  ↓
Uses comparison tool
  ↓
Side-by-side evaluation
  ↓

STEP 5: DECISION & OUTREACH
  ↓
Makes final decision
  ↓
Contacts selected candidate
  ↓

END JOURNEY
```

---

## 🔄 State Management Flow

### **Redux/Zustand State Flow: Interview Start**

```
USER ACTION
  ↓
Component: handleStartInterview(skill)
  ↓
DISPATCH ACTION
  dispatch(startInterview({ skill, candidateId }))
  ↓
STATE MIDDLEWARE
  ├─ Log action (dev tools)
  └─ Process through reducer
  ↓
REDUCER
  interviewSlice.reducer
  ├─ Set isLoading: true
  ├─ Set currentSkill: "React"
  └─ Set interviewStatus: "starting"
  ↓
STATE UPDATED
  state.interview = {
    isLoading: true,
    currentSkill: "React",
    status: "starting"
  }
  ↓
COMPONENT RE-RENDERS
  Shows loading state
  ↓
ASYNC THUNK/ACTION
  startInterviewAsync({ skill, candidateId })
  ├─ API: POST /api/interviews/start
  └─ Wait for response
  ↓
API RESPONSE
  ├─ SUCCESS
  │   ├─ dispatch(startInterviewSuccess(session))
  │   │   ├─ sessionId: "abc123"
  │   │   ├─ status: "in_progress"
  │   │   └─ isLoading: false
  │   └─ Navigate to /interview/abc123
  │
  └─ ERROR
      ├─ dispatch(startInterviewFailure(error))
      │   ├─ error: "Service unavailable"
      │   └─ isLoading: false
      └─ Show error message
  ↓
UI UPDATES
  Shows interview screen or error
```

### **State Structure (Example)**

```javascript
{
  auth: {
    isAuthenticated: true,
    user: {
      id: "user123",
      email: "candidate@example.com",
      role: "candidate",
      walletAddress: "0x..."
    },
    token: "jwt_token_here"
  },
  candidate: {
    profile: {
      name: "John Doe",
      skills: ["React", "Node.js"],
      profileCompletion: 85
    },
    verifications: [
      {
        id: "ver1",
        skill: "React",
        score: 78,
        nftTokenId: "123",
        verifiedAt: "2024-01-15"
      }
    ]
  },
  interview: {
    currentSession: {
      id: "session123",
      skill: "React",
      status: "in_progress",
      question: {...},
      code: "",
      timeElapsed: 1200
    },
    isLoading: false,
    error: null
  },
  employer: {
    subscription: {
      tier: "growth",
      viewsRemaining: 87
    },
    savedSearches: [...],
    candidates: [...]
  }
}
```

---

## 📊 Data Flow Diagrams

### **System Architecture Flow**

```
┌─────────────┐
│   Frontend  │
│  (Next.js)  │
└──────┬──────┘
       │
       │ HTTP/WebSocket
       ↓
┌──────────────────┐
│   API Gateway    │
│    (NestJS)      │
└────────┬─────────┘
         │
         ├──→ Auth Service ──→ PostgreSQL (Users)
         │
         ├──→ Candidate Service ──→ PostgreSQL (Profiles, Verifications)
         │
         ├──→ Interview Service ──→ PostgreSQL (Interviews)
         │                          └──→ AI Service (Python/FastAPI)
         │                              ├──→ LLM APIs (OpenAI)
         │                              └──→ Docker Sandbox (Code Execution)
         │
         ├──→ Employer Service ──→ PostgreSQL (Employers, Searches)
         │
         ├──→ Blockchain Service ──→ Polygon/Base Network
         │                           ├──→ SkillProofNFT Contract
         │                           └──→ Event Listeners
         │
         └──→ IPFS Service ──→ IPFS Network (Metadata, Reports)
         
Redis Cache ←────┘ (Caching layer)
```

### **Interview Flow Data Path**

```
CANDIDATE BROWSER
  ↓
  User clicks "Start Interview"
  ↓
  API: POST /api/interviews/start
  ↓
BACKEND (NestJS)
  ↓
  Create interview session in DB
  ↓
  Call AI Service: Generate Question
  ↓
AI SERVICE (Python/FastAPI)
  ↓
  LLM API Call (OpenAI)
  ↓
  Generate coding question
  ↓
  Return question + test cases
  ↓
BACKEND
  ↓
  Store question in DB
  ↓
  Return to frontend
  ↓
CANDIDATE BROWSER
  ↓
  Display question
  ↓
  User writes code
  ↓
  User clicks "Run Code"
  ↓
  API: POST /api/interviews/:id/execute
  ↓
AI SERVICE
  ↓
  Docker Sandbox
  ↓
  Execute code
  ↓
  Return results
  ↓
BACKEND
  ↓
  Return to frontend
  ↓
CANDIDATE BROWSER
  ↓
  Display test results
  ↓
  User submits final code
  ↓
  API: POST /api/interviews/:id/submit
  ↓
AI SERVICE
  ↓
  Evaluate code (LLM)
  ↓
  Calculate score
  ↓
  Generate report
  ↓
BACKEND
  ↓
  Store results in DB
  ↓
  Upload report to IPFS
  ↓
  Return results
  ↓
CANDIDATE BROWSER
  ↓
  Display score + report
  ↓
  If score >= 70: Show "Mint NFT" button
```

### **NFT Minting Flow Data Path**

```
CANDIDATE BROWSER
  ↓
  User clicks "Mint SkillProof NFT"
  ↓
  Wallet Connection (MetaMask)
  ↓
  Prepare transaction
  ↓
BLOCKCHAIN (Polygon)
  ↓
  Transaction sent
  ↓
  SkillProofNFT.mint() called
  ↓
  NFT minted
  ↓
  Event emitted: SkillProofMinted
  ↓
BACKEND (Event Listener)
  ↓
  Listen to blockchain events
  ↓
  Parse event data
  ↓
  Update database
  ├── Store token ID
  ├── Link to candidate
  └── Update verification record
  ↓
  Send notification to candidate
  ↓
CANDIDATE BROWSER
  ↓
  Transaction confirmed
  ↓
  Refresh dashboard
  ↓
  NFT badge appears
```

---

## 🌳 Decision Trees

### **User Role Decision Tree**

```
USER VISITS WEBSITE
  │
  ├─ Authenticated?
  │   ├─ NO → Show Landing Page
  │   │   └─ User selects role → Signup flow
  │   │
  │   └─ YES → Check role
  │       ├─ Candidate → /dashboard/candidate
  │       └─ Employer → /dashboard/employer
  │
  └─ Accessing protected route?
      ├─ YES → Check authentication
      │   ├─ Not authenticated → Redirect to /login
      │   └─ Authenticated → Check role match
      │       ├─ Match → Allow access
      │       └─ Mismatch → Redirect to correct dashboard
      │
      └─ NO → Allow access (public routes)
```

### **Interview Flow Decision Tree**

```
CANDIDATE WANTS TO VERIFY SKILL
  │
  ├─ Profile complete?
  │   ├─ NO → Prompt to complete profile → END
  │   └─ YES → Continue
  │
  ├─ Free tier limit reached?
  │   ├─ YES → Show upgrade/pay-per-use options
  │   │   ├─ Subscribe → Payment flow
  │   │   ├─ Pay per verification → Payment flow
  │   │   └─ Cancel → END
  │   │
  │   └─ NO → Continue (free attempt available)
  │
  ├─ Start interview
  │   ├─ Generate question (AI)
  │   ├─ User writes code
  │   ├─ User runs tests
  │   └─ User submits
  │
  ├─ AI evaluates
  │   ├─ Calculate score
  │   └─ Generate report
  │
  ├─ Score >= 70?
  │   ├─ YES → Eligible for NFT
  │   │   ├─ Show "Mint NFT" option
  │   │   └─ User can mint
  │   │
  │   └─ NO → Show retry option
  │       └─ User can retry (if attempts remaining)
  │
  └─ NFT minting
      ├─ Wallet connected?
      │   ├─ NO → Prompt to connect → END
      │   └─ YES → Continue
      │
      ├─ Network correct?
      │   ├─ NO → Prompt to switch → END
      │   └─ YES → Continue
      │
      ├─ Approve transaction?
      │   ├─ REJECTED → Show error → END
      │   └─ APPROVED → Mint NFT
      │
      └─ Transaction confirmed?
          ├─ SUCCESS → Update profile → Show badge
          └─ FAILED → Show error → Retry option
```

### **Employer Candidate Evaluation Decision Tree**

```
EMPLOYER VIEWS CANDIDATE SEARCH RESULTS
  │
  ├─ Apply filters?
  │   ├─ YES → Refine search → New results
  │   └─ NO → Continue
  │
  ├─ Click on candidate?
  │   ├─ YES → View profile
  │   │   ├─ Check view limit?
  │   │   │   ├─ Exceeded → Show upgrade prompt
  │   │   │   └─ OK → Decrement count → Show profile
  │   │   │
  │   │   ├─ Verify on blockchain?
  │   │   │   ├─ YES → Query blockchain → Show verification
  │   │   │   └─ NO → Continue
  │   │   │
  │   │   ├─ View report?
  │   │   │   ├─ YES → Check subscription tier
  │   │   │   │   ├─ Has access → Show report
  │   │   │   │   └─ No access → Show upgrade prompt
  │   │   │   └─ NO → Continue
  │   │   │
  │   │   └─ Save candidate?
  │   │       ├─ YES → Add to saved list
  │   │       └─ NO → Continue
  │   │
  │   └─ NO → Continue browsing
  │
  ├─ Compare candidates?
  │   ├─ YES → Select candidates → Comparison view
  │   └─ NO → Continue
  │
  └─ Save search?
      ├─ YES → Save filters → Enable alerts
      └─ NO → Continue
```

---

## 📝 Summary

This complete application workflow guide covers:

1. ✅ **Landing & Onboarding** - User discovery and initial setup
2. ✅ **Authentication** - Email/password and wallet-based auth
3. ✅ **Candidate Workflows** - Profile, interviews, verification
4. ✅ **AI Interview System** - Question generation, evaluation, scoring
5. ✅ **Blockchain Integration** - NFT minting, on-chain verification
6. ✅ **Public Profiles** - Shareable verification credentials
7. ✅ **Employer Workflows** - Search, filter, evaluate candidates
8. ✅ **Monetization** - Subscriptions and pay-per-use
9. ✅ **Notifications** - User engagement and alerts
10. ✅ **Error Handling** - Graceful failure management
11. ✅ **User Journeys** - End-to-end scenarios
12. ✅ **State Management** - Redux/Zustand patterns
13. ✅ **Data Flow** - System architecture and data paths
14. ✅ **Decision Trees** - Key user decision points

---

**END OF COMPLETE APPLICATION WORKFLOW GUIDE**

