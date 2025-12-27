# 📋 Complete Application Workflow Guide - SkillProof

## Table of Contents

1. [Overview](#overview)
2. [Landing Page & Onboarding Workflows](#landing-page--onboarding-workflows)
3. [User Authentication Workflows](#user-authentication-workflows)
4. [Candidate Dashboard Workflows](#candidate-dashboard-workflows)
5. [AI Interview System Workflows](#ai-interview-system-workflows)
6. [Blockchain & SkillProof NFT Workflows](#blockchain--skillproof-nft-workflows)
7. [Public Profile Workflows](#public-profile-workflows)
8. [Employer Dashboard Workflows](#employer-dashboard-workflows)
9. [Monetization & Payment Workflows](#monetization--payment-workflows)
10. [Notification Workflows](#notification-workflows)
11. [Error Handling Workflows](#error-handling-workflows)
12. [Complete User Journeys](#complete-user-journeys)
13. [State Management Flow](#state-management-flow)
14. [Data Flow Diagrams](#data-flow-diagrams)
15. [Decision Trees](#decision-trees)

---

## 🎯 Overview

This document describes the complete workflow of the SkillProof platform - an AI + Blockchain Hiring Trust Platform that verifies developer skills through AI-driven interviews and stores verified proof on blockchain as NFTs.

### Core Concept
- **Candidates**: Take AI coding interviews to verify skills, receive SkillProof NFTs
- **Employers**: Browse verified candidates, view AI reports, verify on-chain credentials

### System Architecture
- **Frontend**: Next.js 14 (App Router), Tailwind CSS, Zustand/Redux, Ethers.js
- **Backend**: Node.js/NestJS, PostgreSQL, Redis
- **AI Service**: Python/FastAPI, LLM APIs, Docker sandbox
- **Blockchain**: Solidity, Polygon/Base, ERC-721 NFTs

---

## 🏠 Landing Page & Onboarding Workflows

### 0. **Landing Page Flow**

```
START
  ↓
User visits website (root URL: /)
  ↓
CHECK: User authenticated?
  ├─ YES → CHECK: User role
  │   ├─ Candidate → Redirect to /dashboard/candidate
  │   └─ Employer → Redirect to /dashboard/employer
  │
  └─ NO → Continue
  ↓
Display Landing Page
  ├─ Hero section: "The Trust Layer for Remote Hiring"
  ├─ Value proposition cards
  ├─ Feature highlights (AI Interview, Blockchain Verification, etc.)
  ├─ CTA buttons: "Get Verified" (Candidate) | "Find Talent" (Employer)
  └─ Header shows: Login | Sign Up (no profile icon)
  ↓
User actions
  ├─ Click "Get Verified" → Role: Candidate → Navigate to /signup?role=candidate
  ├─ Click "Find Talent" → Role: Employer → Navigate to /signup?role=employer
  ├─ Click "Login" → Navigate to /login
  └─ Click Logo → Stay on landing page
  ↓
END
```

### 1. **User Role Selection Flow**

```
START
  ↓
User clicks signup CTA
  ↓
Show role selection modal/screen
  ├─ Option A: "I'm a Developer" (Candidate)
  ├─ Option B: "I'm Hiring" (Employer)
  └─ [Cancel]
  ↓
User selects role
  ├─ Candidate → Store role in state → Navigate to /signup?role=candidate
  └─ Employer → Store role in state → Navigate to /signup?role=employer
  ↓
END
```

### 2. **Route Protection Flow**

```
START
  ↓
User tries to access protected route
  ├─ /dashboard/candidate
  ├─ /dashboard/employer
  ├─ /interview/:skillId
  ├─ /profile
  └─ /settings
  ↓
ProtectedRoute component checks authentication
  ├─ Get isAuthenticated from Redux/Zustand state
  ├─ Get user role from state
  └─ Check: isAuthenticated === true?
  ↓
AUTHENTICATED?
  ├─ YES → CHECK: Correct role for route?
  │   ├─ Route requires Candidate + User is Candidate → Allow access
  │   ├─ Route requires Employer + User is Employer → Allow access
  │   ├─ Route requires Candidate + User is Employer → Redirect to /dashboard/employer
  │   └─ Route requires Employer + User is Candidate → Redirect to /dashboard/candidate
  │
  └─ NO → Redirect to /login
      ├─ Store original route in location.state
      ├─ Navigate to /login
      └─ Show login page
  ↓
User logs in successfully
  ↓
Login page reads location.state.from
  ├─ If exists → Redirect to original route
  └─ If not → Redirect to default dashboard (based on role)
  ↓
END
```

---

## 🔐 User Authentication Workflows

### 1. **Candidate Registration Flow (Email/Password)**

```
START
  ↓
User navigates to /signup?role=candidate
  ↓
Display Candidate Registration Form
  ├─ Name (required)
  ├─ Email (required)
  ├─ Password (required, min 8 chars)
  ├─ Confirm Password (required)
  ├─ Role: Candidate (pre-filled, hidden)
  └─ Terms & Conditions checkbox
  ↓
User fills form and clicks "Sign Up"
  ↓
VALIDATION CHECK
  ├─ Email format valid? → NO → Show error → BACK TO FORM
  ├─ Password strength OK? → NO → Show error → BACK TO FORM
  ├─ Passwords match? → NO → Show error → BACK TO FORM
  ├─ Terms accepted? → NO → Show error → BACK TO FORM
  └─ Email already exists? → YES → Show error → BACK TO FORM
  ↓
ALL VALIDATIONS PASS → YES
  ↓
Redux Action: signupUser(userData) dispatched
  ↓
API Call: POST /api/auth/signup
  ├─ Hash password (bcrypt)
  ├─ Create user in database (role: candidate)
  ├─ Generate JWT token
  ├─ Generate email verification token
  └─ Send verification email
  ↓
API Response Received
  ├─ SUCCESS (200)
  │   ├─ Store token in localStorage
  │   ├─ Update Redux: signupSuccess(user)
  │   ├─ Show success message
  │   └─ Redirect to email verification page or dashboard
  │
  └─ ERROR (400/500)
      ├─ Update Redux: signupFailure(error)
      ├─ Show error message
      └─ Stay on signup page
  ↓
END
```

### 2. **Employer Registration Flow (Email/Password)**

```
START
  ↓
User navigates to /signup?role=employer
  ↓
Display Employer Registration Form
  ├─ Company Name (required)
  ├─ Your Name (required)
  ├─ Business Email (required)
  ├─ Password (required)
  ├─ Confirm Password (required)
  ├─ Role: Employer (pre-filled, hidden)
  └─ Terms & Conditions checkbox
  ↓
User fills form and clicks "Sign Up"
  ↓
VALIDATION CHECK
  ├─ Email format valid? → NO → Show error → BACK TO FORM
  ├─ Company name filled? → NO → Show error → BACK TO FORM
  └─ All other validations same as candidate
  ↓
ALL VALIDATIONS PASS → YES
  ↓
API Call: POST /api/auth/signup
  ├─ Hash password
  ├─ Create user in database (role: employer)
  ├─ Create employer profile record
  ├─ Generate JWT token
  └─ Send welcome email
  ↓
SUCCESS
  ├─ Store token
  ├─ Update Redux: signupSuccess(user)
  └─ Redirect to /dashboard/employer (or subscription selection)
  ↓
END
```

### 3. **Wallet-based Authentication Flow (Web3)**

```
START
  ↓
User clicks "Connect Wallet" on signup/login page
  ↓
CHECK: Wallet installed?
  ├─ NO → Show install instructions (MetaMask, WalletConnect, etc.) → END
  └─ YES → Continue
  ↓
Show wallet options
  ├─ MetaMask
  ├─ WalletConnect
  ├─ Coinbase Wallet
  └─ [Cancel]
  ↓
User selects wallet
  ↓
Request wallet connection
  ↓
User approves connection in wallet
  ├─ REJECTED → Show message → END
  └─ APPROVED → Continue
  ↓
Get wallet address
  ↓
CHECK: Address already registered?
  ├─ YES → Login user → Redirect to dashboard
  └─ NO → Continue registration
  ↓
Request signature for authentication
  ├─ Message: "Sign in to SkillProof\n\nWallet: {address}\nNonce: {random}"
  └─ Nonce: random string (store on backend)
  ↓
User signs message in wallet
  ├─ REJECTED → Show error → END
  └─ APPROVED → Continue
  ↓
API Call: POST /api/auth/web3-signup or /api/auth/web3-login
  ├─ Verify signature
  ├─ Check nonce validity
  ├─ If new: Create user with wallet address
  ├─ If existing: Link wallet to account (if not already linked)
  └─ Generate JWT token
  ↓
SUCCESS
  ├─ Store token
  ├─ Store wallet address in Redux
  ├─ Update Redux: loginSuccess(user)
  └─ Redirect to dashboard (based on role)
  ↓
END
```

### 4. **User Login Flow (Email/Password)**

```
START
  ↓
User navigates to Login page (/login)
  ↓
User enters credentials
  ├─ Email/Username
  └─ Password
  ↓
User clicks "Login"
  ↓
VALIDATION
  ├─ Fields filled? → NO → Show error → BACK
  └─ YES → Continue
  ↓
Redux Action: loginUser(credentials) dispatched
  ↓
Redux State: isLoading = true
  ↓
API Call: POST /api/auth/login
  ├─ Find user by email
  ├─ Compare password hash
  ├─ Get user role
  └─ Generate JWT token
  ↓
API Response
  ├─ SUCCESS (200)
  │   ├─ Token received
  │   ├─ Store token in localStorage
  │   ├─ Store refresh token (if applicable)
  │   ├─ Update Redux: loginSuccess(user)
  │   │   ├─ user: userData (with role)
  │   │   ├─ isAuthenticated: true
  │   │   └─ isLoading: false
  │   ├─ Fetch user profile
  │   ├─ Redirect based on role:
  │   │   ├─ Candidate → /dashboard/candidate
  │   │   └─ Employer → /dashboard/employer
  │   └─ If location.state.from exists → Redirect there
  │
  └─ ERROR (401)
      ├─ Update Redux: loginFailure(error)
      │   ├─ error: "Invalid credentials"
      │   └─ isLoading: false
      ├─ Show error message
      └─ Stay on login page
  ↓
END
```

---

## 👤 Candidate Dashboard Workflows

### 1. **Candidate Dashboard Overview Flow**

```
START
  ↓
User logs in as Candidate
  ↓
Navigate to /dashboard/candidate
  ↓
ProtectedRoute checks: isAuthenticated && role === 'candidate'
  ↓
Fetch candidate data
  ├─ API: GET /api/candidate/profile
  ├─ API: GET /api/candidate/verifications
  └─ API: GET /api/candidate/stats
  ↓
Display Dashboard
  ├─ Header: Profile Icon, Settings, Logout
  ├─ Profile completion status (progress bar)
  ├─ Verified Skills Section
  │   ├─ List of verified skills with scores
  │   ├─ SkillProof NFT badges
  │   └─ "Verify New Skill" button
  ├─ Recent Activity
  │   ├─ Recent verifications
  │   └─ Interview history
  ├─ Quick Stats
  │   ├─ Total verified skills
  │   ├─ Average score
  │   └─ Public profile views
  └─ Action Cards
      ├─ Complete Profile
      ├─ Start Verification
      └─ View Public Profile
  ↓
END
```

### 2. **Profile Creation Flow**

```
START
  ↓
Candidate clicks "Complete Profile" or navigates to /profile/setup
  ↓
Display Profile Setup Wizard (Multi-step)
  ↓
STEP 1: Basic Information
  ├─ Full Name
  ├─ Location
  ├─ Bio/Summary
  ├─ LinkedIn URL (optional)
  └─ Portfolio URL (optional)
  ↓
User clicks "Next"
  ↓
STEP 2: Skills Selection
  ├─ Select skills from list (React, Node.js, Python, etc.)
  ├─ Add custom skills
  └─ Set proficiency level (if applicable)
  ↓
User clicks "Next"
  ↓
STEP 3: Resume Upload
  ├─ Upload resume (PDF/DOCX)
  ├─ AI parses resume
  ├─ Show parsed skills/experience
  └─ User confirms or edits
  ↓
User clicks "Next"
  ↓
STEP 4: GitHub Integration (Optional)
  ├─ Enter GitHub username
  ├─ Connect GitHub account (OAuth)
  ├─ Fetch GitHub stats (repos, commits, languages)
  └─ Show preview
  ↓
User clicks "Save Profile"
  ↓
VALIDATION
  ├─ Required fields filled? → NO → Show error → BACK
  └─ YES → Continue
  ↓
API Call: POST /api/candidate/profile
  ├─ Save profile data
  ├─ Process resume (store in S3/IPFS)
  ├─ Update GitHub stats
  └─ Calculate profile completion %
  ↓
SUCCESS
  ├─ Update Redux: updateProfile(profile)
  ├─ Show success message
  └─ Redirect to /dashboard/candidate
  ↓
END
```

### 3. **Resume Upload & Parsing Flow**

```
START
  ↓
User selects resume file (PDF/DOCX)
  ↓
File validation
  ├─ File size < 10MB? → NO → Show error → END
  ├─ File type valid? → NO → Show error → END
  └─ YES → Continue
  ↓
Upload file to temporary storage
  ↓
Show loading: "Parsing resume..."
  ↓
API Call: POST /api/candidate/resume/parse
  ├─ Extract text from PDF/DOCX
  ├─ Send to AI Service for parsing
  └─ AI Service returns:
      ├─ Skills detected
      ├─ Experience (companies, roles, dates)
      ├─ Education
      └─ Summary
  ↓
API Response
  ├─ SUCCESS (200)
  │   ├─ Display parsed data in form
  │   ├─ Pre-fill skills list
  │   ├─ Pre-fill experience fields
  │   ├─ User can edit/modify
  │   └─ User clicks "Save"
  │       ↓
  │       API: POST /api/candidate/profile
  │       ├─ Save resume file to permanent storage (S3/IPFS)
  │       ├─ Store resume metadata
  │       └─ Update profile
  │
  └─ ERROR (400/500)
      ├─ Show error message
      └─ Allow manual entry
  ↓
END
```

### 4. **GitHub Integration Flow**

```
START
  ↓
User clicks "Connect GitHub" in profile setup
  ↓
Two options:
  ├─ Option A: Enter GitHub username (read-only)
  │   ├─ User enters username
  │   ├─ API: GET /api/candidate/github/stats?username=xxx
  │   ├─ Fetch public repos, commits, languages
  │   └─ Display stats preview
  │
  └─ Option B: OAuth connection (full access)
      ├─ User clicks "Connect with GitHub"
      ├─ Redirect to GitHub OAuth
      ├─ User authorizes SkillProof
      ├─ GitHub redirects back with code
      ├─ Exchange code for access token
      ├─ Fetch GitHub data (repos, commits, languages, contributions)
      └─ Store access token (encrypted)
  ↓
Display GitHub stats
  ├─ Repository count
  ├─ Total commits
  ├─ Languages used
  ├─ Contribution graph
  └─ Top repositories
  ↓
User confirms integration
  ↓
API Call: POST /api/candidate/github/link
  ├─ Store GitHub username/token
  ├─ Store GitHub stats in database
  └─ Update profile
  ↓
SUCCESS
  ├─ Show "GitHub Connected" badge
  └─ Refresh dashboard
  ↓
END
```

---

## 🤖 AI Interview System Workflows

### 1. **Interview Initiation Flow**

```
START
  ↓
Candidate selects skill to verify (e.g., "React")
  ↓
CHECK: Profile complete enough?
  ├─ NO → Prompt to complete profile → END
  └─ YES → Continue
  ↓
CHECK: Free tier limits
  ├─ Free user + Already used this month? → Show upgrade prompt → END
  └─ YES (has free attempt) or Paid user → Continue
  ↓
Show Interview Preparation Screen
  ├─ Skill: React
  ├─ Estimated duration: 30-45 minutes
  ├─ Instructions:
  │   ├─ No copy-paste allowed
  │   ├─ No external help
  │   ├─ Real-time code execution
  │   └─ AI will evaluate your solution
  ├─ "Practice Mode" button (if available)
  └─ "Start Interview" button
  ↓
User clicks "Start Interview"
  ↓
CHECK: User ready?
  ├─ Practice mode → Start practice interview (no NFT minting)
  └─ Real interview → Continue
  ↓
Create interview session
  ├─ API: POST /api/interviews/start
  ├─ Generate unique session ID
  ├─ Store session start time
  ├─ Set status: "in_progress"
  └─ Return session ID
  ↓
Navigate to /interview/:sessionId
  ↓
END
```

### 2. **Question Generation Flow**

```
START
  ↓
Interview session started
  ↓
API Call: POST /api/interviews/:sessionId/generate-question
  ├─ Get candidate profile (skills, experience level)
  ├─ Get selected skill to verify
  ├─ Determine difficulty level:
  │   ├─ Based on resume experience
  │   ├─ Based on GitHub activity
  │   └─ Default: Intermediate
  └─ Send to AI Service
  ↓
AI Service (Python/FastAPI)
  ├─ Prompt: "Generate coding question for {skill} at {difficulty} level"
  ├─ LLM generates question
  ├─ Generate test cases
  ├─ Generate expected outputs
  └─ Return:
      ├─ Question text
      ├─ Problem description
      ├─ Example inputs/outputs
      ├─ Constraints
      └─ Difficulty metadata
  ↓
API Response
  ├─ SUCCESS (200)
  │   ├─ Display question in interview UI
  │   ├─ Show code editor (Monaco/CodeMirror)
  │   ├─ Show test cases section
  │   ├─ Start timer
  │   └─ Enable code submission
  │
  └─ ERROR (500)
      ├─ Show error message
      ├─ Retry button
      └─ Or fallback to pre-defined questions
  ↓
END
```

### 3. **Code Execution & Evaluation Flow**

```
START
  ↓
Candidate writes code in editor
  ↓
Candidate clicks "Run Code" or "Test Solution"
  ↓
VALIDATION
  ├─ Code not empty? → NO → Show error → BACK
  └─ YES → Continue
  ↓
Capture code snapshot
  ├─ Code content
  ├─ Language
  └─ Timestamp
  ↓
Send to AI Service for execution
  ├─ API: POST /api/interviews/:sessionId/execute
  ├─ Code sent to Docker sandbox
  └─ Execute code with test cases
  ↓
Docker Sandbox Execution
  ├─ Create isolated container
  ├─ Run code with test inputs
  ├─ Capture stdout, stderr
  ├─ Measure execution time
  ├─ Check memory usage
  └─ Timeout after 10 seconds
  ↓
Return execution results
  ├─ Test case results (pass/fail)
  ├─ Output for each test
  ├─ Execution time
  └─ Any errors
  ↓
Display results to candidate
  ├─ Show which tests passed/failed
  ├─ Show output comparisons
  └─ Allow candidate to revise code
  ↓
Candidate can:
  ├─ Revise code → Run again
  └─ Submit final solution
  ↓
END
```

### 4. **Code Submission & AI Evaluation Flow**

```
START
  ↓
Candidate clicks "Submit Solution"
  ↓
Show confirmation dialog
  ├─ "Are you sure? You cannot modify after submission."
  └─ [Cancel] [Submit]
  ↓
User confirms submission
  ↓
Capture final code state
  ├─ Code content
  ├─ Number of attempts
  ├─ Total time taken
  ├─ Number of test runs
  └─ Timestamps (start, end)
  ↓
Run fraud detection checks
  ├─ Time patterns analysis
  ├─ Copy-paste detection (if applicable)
  ├─ Suspicious behavior flags
  └─ Flag if needed
  ↓
Send to AI Service for evaluation
  ├─ API: POST /api/interviews/:sessionId/evaluate
  ├─ Send code, test results, metadata
  └─ AI Service evaluates:
      ├─ Correctness (test case results)
      ├─ Code quality
      │   ├─ Readability
      │   ├─ Best practices
      │   ├─ Code organization
      │   └─ Error handling
      ├─ Time complexity analysis
      ├─ Space complexity analysis
      └─ Overall assessment
  ↓
AI Service Processing
  ├─ LLM analyzes code
  ├─ Generates detailed feedback
  ├─ Calculates score (0-100)
  └─ Returns evaluation report
  ↓
API Response
  ├─ SUCCESS (200)
  │   ├─ Score: 78/100 (example)
  │   ├─ Detailed feedback
  │   ├─ Strengths identified
  │   ├─ Areas for improvement
  │   ├─ Code quality metrics
  │   └─ Store evaluation in database
  │
  └─ ERROR (500)
      ├─ Show error message
      └─ Manual review queue (fallback)
  ↓
Update interview session
  ├─ Status: "completed"
  ├─ Score stored
  ├─ Evaluation report stored
  └─ Completion timestamp
  ↓
Navigate to results page
  ↓
END
```

### 5. **Scoring & Report Generation Flow**

```
START
  ↓
Interview evaluation completed
  ↓
Calculate normalized score (0-100)
  ├─ Test case correctness: 40% weight
  ├─ Code quality: 30% weight
  ├─ Complexity analysis: 20% weight
  ├─ Best practices: 10% weight
  └─ Apply fraud penalty (if applicable)
  ↓
Generate interview report
  ├─ Candidate name
  ├─ Skill tested
  ├─ Score (78/100)
  ├─ Date completed
  ├─ Detailed feedback sections:
  │   ├─ Solution correctness
  │   ├─ Code quality analysis
  │   ├─ Performance analysis
  │   ├─ Strengths
  │   └─ Improvement suggestions
  ├─ Code submission (with syntax highlighting)
  └─ Execution results
  ↓
Store report
  ├─ Save to database
  ├─ Generate PDF version
  ├─ Store PDF in S3/IPFS
  └─ Generate IPFS hash (for blockchain)
  ↓
Check score threshold
  ├─ Score >= 70? → YES → Eligible for NFT minting
  └─ Score < 70? → NO → Show "Retry available" message
  ↓
Display results page
  ├─ Show score prominently
  ├─ Show report preview
  ├─ "View Full Report" button
  ├─ "Mint SkillProof NFT" button (if eligible)
  └─ "Share Profile" button
  ↓
END
```

---

*This is Part 1 of the workflow document. Part 2 will continue with Blockchain/NFT workflows, Employer workflows, and remaining sections.*

