# 📋 Complete Application Workflow Guide - SkillProof (Part 2)

*Continuing from Part 1...*

---

## ⛓️ Blockchain & SkillProof NFT Workflows

### 1. **Wallet Connection Flow**

```
START
  ↓
Candidate has completed interview with score >= 70
  ↓
Candidate clicks "Mint SkillProof NFT"
  ↓
CHECK: Wallet connected?
  ├─ NO → Show wallet connection modal
  │   ├─ Options: MetaMask, WalletConnect, Coinbase
  │   ├─ User selects wallet
  │   ├─ Request connection
  │   └─ User approves → Continue
  │
  └─ YES → Continue
  ↓
CHECK: Network correct?
  ├─ Current network is Polygon/Base? → YES → Continue
  └─ NO → Show switch network prompt
      ├─ "Please switch to Polygon network"
      └─ User switches → Continue
  ↓
Get wallet address
  ↓
Prepare NFT metadata
  ├─ Candidate wallet: {address}
  ├─ Skill: "React"
  ├─ Score: 78
  ├─ Score hash: hash(score + report + timestamp)
  ├─ Issued at: timestamp
  ├─ Interview report IPFS hash: {hash}
  └─ Issuer: "SkillProof Platform"
  ↓
Generate token URI
  ├─ Upload metadata to IPFS (or prepare for on-chain)
  └─ Get IPFS hash/URI
  ↓
Prepare minting transaction
  ├─ Contract: SkillProofNFT.sol
  ├─ Function: mintSkillProof(metadata)
  ├─ Gas estimation
  └─ Show transaction preview
  ↓
END
```

### 2. **NFT Minting Process Flow**

```
START
  ↓
Transaction preview displayed
  ├─ Skill: React
  ├─ Score: 78/100
  ├─ Estimated gas fee: 0.01 MATIC (example)
  └─ Network: Polygon
  ↓
CHECK: Gasless minting available?
  ├─ YES (sponsored by employer/platform) → Continue without gas
  └─ NO → Request transaction approval
  ↓
User approves transaction
  ├─ REJECTED → Show message → END
  └─ APPROVED → Continue
  ↓
Transaction sent to blockchain
  ↓
Show loading: "Minting SkillProof NFT..."
  ├─ Transaction hash displayed
  ├─ Link to block explorer
  └─ Progress indicator
  ↓
Wait for transaction confirmation
  ├─ Pending → Show status
  ├─ Confirmed → Continue
  └─ Failed → Show error → END
  ↓
Smart contract executes
  ├─ SkillProofNFT.mint()
  ├─ Validates: Only platform can mint (access control)
  ├─ Mints NFT to candidate's wallet
  ├─ Sets token URI (IPFS metadata)
  ├─ Stores skill and score hash on-chain
  └─ Emits SkillProofMinted event
  ↓
Transaction confirmed
  ↓
Backend listens to blockchain event
  ├─ Parse SkillProofMinted event
  ├─ Extract token ID
  ├─ Store NFT details in database
  │   ├─ Token ID
  │   ├─ Transaction hash
  │   ├─ Block number
  │   ├─ Skill verified
  │   └─ Link to candidate profile
  └─ Update candidate's verification record
  ↓
SUCCESS
  ├─ Show success notification: "SkillProof NFT Minted!"
  ├─ Display NFT badge
  ├─ Show token ID
  ├─ Link to view on OpenSea/Polygonscan
  ├─ Update Redux: addVerification(verification)
  └─ Refresh dashboard
  ↓
END
```

### 3. **On-chain Verification Flow**

```
START
  ↓
Employer viewing candidate profile
  ↓
Profile displays verified skills with NFT badges
  ↓
Employer clicks "Verify on Blockchain" on a skill
  ↓
Fetch NFT details
  ├─ Token ID from database
  ├─ Contract address
  └─ Candidate wallet address
  ↓
Query blockchain
  ├─ Call: SkillProofNFT.ownerOf(tokenId)
  ├─ Call: SkillProofNFT.tokenURI(tokenId)
  └─ Verify: owner matches candidate wallet
  ↓
On-chain verification check
  ├─ Owner matches? → YES → Continue
  └─ NO → Show "Verification failed" → END
  ↓
Fetch metadata from IPFS
  ├─ Get IPFS hash from token URI
  ├─ Fetch metadata JSON
  └─ Parse metadata
  ↓
Compare metadata
  ├─ Skill name matches
  ├─ Score hash matches
  ├─ Issuer is SkillProof Platform
  └─ Timestamp is valid
  ↓
Display verification result
  ├─ ✅ "Verified on Blockchain"
  ├─ Network: Polygon
  ├─ Transaction hash
  ├─ Block number
  ├─ Token ID
  ├─ View on explorer link
  └─ ✅ "This credential is authentic and tamper-proof"
  ↓
END
```

### 4. **IPFS Metadata Storage Flow**

```
START
  ↓
Interview completed, score calculated
  ↓
Prepare metadata JSON
  ├─ {
  │   "name": "SkillProof: React",
  │   "description": "Verified React developer skill",
  │   "image": "https://ipfs.io/ipfs/{badge-image-hash}",
  │   "attributes": [
  │     { "trait_type": "Skill", "value": "React" },
  │     { "trait_type": "Score", "value": 78 },
  │     { "trait_type": "Date", "value": "2024-01-15" }
  │   ],
  │   "properties": {
  │     "candidateWallet": "0x...",
  │     "scoreHash": "0x...",
  │     "reportHash": "ipfs://{report-hash}",
  │     "issuedAt": timestamp,
  │     "issuer": "SkillProof"
  │   }
  │ }
  ↓
Upload metadata to IPFS
  ├─ API: POST /api/ipfs/upload
  ├─ Pin metadata to IPFS
  └─ Receive IPFS hash (CID)
  ↓
Upload interview report PDF to IPFS
  ├─ Generate PDF report
  ├─ Upload to IPFS
  └─ Receive report IPFS hash
  ↓
Store IPFS hashes
  ├─ Metadata hash → Use as token URI
  ├─ Report hash → Store in metadata.properties
  └─ Save to database
  ↓
NFT metadata includes IPFS links
  ├─ tokenURI: "ipfs://{metadata-hash}"
  └─ Report accessible via metadata.properties.reportHash
  ↓
END
```

---

## 🌐 Public Profile Workflows

### 1. **Public Profile Generation Flow**

```
START
  ↓
Candidate has at least one verified skill
  ↓
Candidate navigates to profile settings
  ↓
Generate public profile URL
  ├─ Format: skillproof.io/profile/{unique-id}
  ├─ unique-id: UUID or username-based
  └─ Store in database
  ↓
Candidate enables public profile
  ├─ Toggle: "Make profile public"
  ├─ Optional: Enable anonymous mode
  └─ Save preferences
  ↓
API Call: POST /api/candidate/profile/public
  ├─ Enable public profile
  ├─ Generate shareable link
  └─ Update profile settings
  ↓
Profile becomes publicly accessible
  ├─ URL is shareable
  ├─ No authentication required to view
  └─ Displays verified skills only
  ↓
END
```

### 2. **Public Profile Display Flow**

```
START
  ↓
User visits public profile URL: /profile/{profileId}
  ↓
No authentication required
  ↓
API Call: GET /api/public/profile/{profileId}
  ├─ Fetch public profile data
  ├─ Only return public information:
  │   ├─ Verified skills (with scores)
  │   ├─ SkillProof NFT badges
  │   ├─ Verification dates
  │   ├─ Public bio (if enabled)
  │   └─ Anonymous mode settings
  └─ Exclude: Email, wallet address, private info
  ↓
Display Public Profile
  ├─ Profile header
  │   ├─ Name (or "Anonymous Developer" if anonymous)
  │   ├─ Location (if public)
  │   └─ Bio (if public)
  ├─ Verified Skills Section
  │   ├─ List of verified skills
  │   ├─ Score for each skill
  │   ├─ Verification date
  │   └─ SkillProof NFT badge (clickable)
  ├─ Blockchain Verification Section
  │   ├─ "Verified on Blockchain" badge
  │   ├─ Network: Polygon
  │   └─ View on explorer links
  └─ Share buttons
      ├─ Copy link
      ├─ Share on LinkedIn
      └─ Share on Twitter
  ↓
For each verified skill:
  ↓
CHECK: NFT ownership verification
  ├─ Query blockchain: ownerOf(tokenId)
  ├─ Verify owner matches profile
  └─ Display "✅ Verified on-chain" badge
  ↓
END
```

### 3. **Profile Sharing Flow**

```
START
  ↓
Candidate on public profile page
  ↓
Candidate clicks "Share Profile"
  ↓
Show sharing options
  ├─ Copy link
  ├─ Share on LinkedIn
  ├─ Share on Twitter
  ├─ Share on Facebook
  └─ Generate QR code
  ↓
User selects option
  ├─ Copy link
  │   ├─ Copy URL to clipboard
  │   ├─ Show "Link copied!" toast
  │   └─ User can paste anywhere
  │
  ├─ Share on LinkedIn
  │   ├─ Generate LinkedIn share URL
  │   ├─ Open LinkedIn share dialog
  │   └─ Pre-filled text: "Check out my verified skills on SkillProof!"
  │
  └─ Share on Twitter
      ├─ Generate Twitter share URL
      ├─ Open Twitter compose
      └─ Pre-filled text: "Just verified my React skills! 🎉 Check it out: {link}"
  ↓
END
```

---

## 🏢 Employer Dashboard Workflows

### 1. **Employer Dashboard Overview Flow**

```
START
  ↓
Employer logs in
  ↓
Navigate to /dashboard/employer
  ↓
ProtectedRoute checks: isAuthenticated && role === 'employer'
  ↓
Fetch employer data
  ├─ API: GET /api/employer/profile
  ├─ API: GET /api/employer/stats
  └─ API: GET /api/candidates/search?limit=20 (preview)
  ↓
Display Employer Dashboard
  ├─ Header: Profile, Settings, Logout
  ├─ Quick Stats
  │   ├─ Candidates viewed this month
  │   ├─ Searches performed
  │   ├─ Saved candidates
  │   └─ Subscription status
  ├─ Search Candidates Section
  │   ├─ Search bar
  │   ├─ Filters (skills, score range, etc.)
  │   └─ "Advanced Search" button
  ├─ Recent Candidates (preview)
  │   ├─ Cards with verified skills
  │   ├─ Scores displayed
  │   └─ "View Profile" buttons
  └─ Action Cards
      ├─ Upgrade Subscription
      ├─ Saved Searches
      └─ Candidate Comparison
  ↓
END
```

### 2. **Candidate Search & Discovery Flow**

```
START
  ↓
Employer on dashboard
  ↓
Employer enters search query
  ├─ Skill name (e.g., "React")
  ├─ Location (optional)
  └─ Minimum score (optional)
  ↓
Employer applies filters
  ├─ Skills: [React, Node.js]
  ├─ Score range: 70-100
  ├─ Verification status: Verified only
  ├─ Date verified: Last 6 months
  └─ Location: Remote, US, etc.
  ↓
Employer clicks "Search" or filters auto-apply
  ↓
API Call: GET /api/candidates/search
  ├─ Query parameters:
  │   ├─ skills: ["React", "Node.js"]
  │   ├─ minScore: 70
  │   ├─ maxScore: 100
  │   ├─ verified: true
  │   ├─ dateRange: "6months"
  │   └─ location: "remote"
  ├─ Database query with filters
  ├─ Join with verification records
  ├─ Filter by public profiles only
  └─ Return paginated results
  ↓
API Response
  ├─ SUCCESS (200)
  │   ├─ Results array (20 per page)
  │   ├─ Total count
  │   ├─ Page number
  │   └─ For each candidate:
  │       ├─ Profile ID
  │       ├─ Name (or anonymous)
  │       ├─ Verified skills with scores
  │       ├─ Verification dates
  │       ├─ Public profile link
  │       └─ NFT verification status
  │
  └─ ERROR (500)
      ├─ Show error message
      └─ Display cached results (if any)
  ↓
Display search results
  ├─ Candidate cards in grid/list view
  ├─ Each card shows:
  │   ├─ Name
  │   ├─ Verified skills badges
  │   ├─ Scores
  │   ├─ "View Profile" button
  │   └─ "Save Candidate" button
  ├─ Pagination controls
  └─ "Save Search" button
  ↓
END
```

### 3. **Candidate Profile View Flow (Employer)**

```
START
  ↓
Employer clicks "View Profile" on candidate card
  ↓
Navigate to /candidates/{profileId}
  ↓
API Call: GET /api/employer/candidates/{profileId}
  ├─ Check employer subscription status
  ├─ Check view limits (if free/starter tier)
  ├─ Fetch candidate public profile
  ├─ Fetch verification records
  └─ Return detailed data
  ↓
CHECK: View limit exceeded?
  ├─ YES → Show upgrade prompt → END
  └─ NO → Continue (or decrement view count)
  ↓
Display Candidate Detail Page
  ├─ Profile Header
  │   ├─ Name
  │   ├─ Location
  │   ├─ Bio
  │   └─ Public links (GitHub, LinkedIn, Portfolio)
  ├─ Verified Skills Section
  │   ├─ Each skill:
  │   │   ├─ Skill name
  │   │   ├─ Score (e.g., 78/100)
  │   │   ├─ Verification date
  │   │   ├─ SkillProof NFT badge
  │   │   ├─ "Verify on Blockchain" button
  │   │   └─ "View Report" button
  │   └─ Blockchain verification status
  ├─ GitHub Stats (if available)
  │   ├─ Repository count
  │   ├─ Languages
  │   └─ Contribution graph
  └─ Action Buttons
      ├─ Save Candidate
      ├─ Contact (if enabled)
      ├─ Download Resume (if available)
      └─ Compare with Others
  ↓
END
```

### 4. **Interview Report Viewing Flow**

```
START
  ↓
Employer viewing candidate profile
  ↓
Employer clicks "View Report" on a verified skill
  ↓
CHECK: Employer subscription tier
  ├─ Free/Starter → Show upgrade prompt → END
  └─ Growth/Enterprise → Continue
  ↓
API Call: GET /api/employer/reports/{verificationId}
  ├─ Verify employer has access
  ├─ Fetch interview report
  ├─ Include:
  │   ├─ Full evaluation
  │   ├─ Code submission
  │   ├─ Test results
  │   ├─ AI feedback
  │   └─ Score breakdown
  └─ Return report data
  ↓
Display Report Modal/Page
  ├─ Report Header
  │   ├─ Skill: React
  │   ├─ Score: 78/100
  │   ├─ Date: 2024-01-15
  │   └─ Duration: 35 minutes
  ├─ Score Breakdown
  │   ├─ Test cases: 32/40
  │   ├─ Code quality: 24/30
  │   ├─ Complexity: 15/20
  │   └─ Best practices: 7/10
  ├─ Detailed Feedback
  │   ├─ Strengths
  │   ├─ Areas for improvement
  │   └─ Code quality analysis
  ├─ Code Submission
  │   ├─ Syntax highlighted code
  │   └─ Execution results
  └─ Action Buttons
      ├─ Download PDF
      └─ Share Report (internal)
  ↓
END
```

### 5. **Saved Searches & Alerts Flow**

```
START
  ↓
Employer has applied search filters
  ↓
Employer clicks "Save Search"
  ↓
Show save search dialog
  ├─ Search name: "Senior React Developers"
  ├─ Current filters displayed
  └─ Alert options:
      ├─ Email notifications
      ├─ Daily digest
      └─ Weekly summary
  ↓
User enters search name and selects alert preferences
  ↓
User clicks "Save"
  ↓
API Call: POST /api/employer/searches/save
  ├─ Save search filters
  ├─ Save alert preferences
  └─ Return saved search ID
  ↓
SUCCESS
  ├─ Show "Search saved!" message
  └─ Search appears in "Saved Searches" section
  ↓
Background Process: Search Monitoring
  ├─ Daily check: New candidates matching saved search
  ├─ If new matches found:
  │   ├─ Send email notification (if enabled)
  │   ├─ Update "New matches" count
  │   └─ Show in-app notification
  └─ Employer can view new matches
  ↓
END
```

### 6. **Candidate Comparison Tool Flow**

```
START
  ↓
Employer viewing candidate profiles
  ↓
Employer selects candidates to compare
  ├─ Checkbox on candidate cards
  └─ Or "Compare" button on profile
  ↓
Selected candidates added to comparison list
  ├─ Shows in comparison sidebar
  └─ "Compare (3)" button
  ↓
Employer clicks "Compare"
  ↓
Navigate to /employer/compare?ids=id1,id2,id3
  ↓
API Call: GET /api/employer/candidates/compare?ids=...
  ├─ Fetch all candidate profiles
  ├─ Fetch verification records
  └─ Return comparison data
  ↓
Display Comparison View
  ├─ Side-by-side candidate columns
  ├─ For each candidate:
  │   ├─ Profile summary
  │   ├─ Verified skills (with scores)
  │   ├─ Average score
  │   ├─ Verification dates
  │   └─ GitHub stats
  ├─ Highlight differences
  └─ Action buttons:
      ├─ Export comparison (PDF/CSV)
      ├─ Save comparison
      └─ Contact candidates
  ↓
END
```

---

## 💰 Monetization & Payment Workflows

### 1. **Candidate Subscription/Payment Flow**

```
START
  ↓
Candidate tries to verify skill (free tier limit reached)
  OR
Candidate navigates to /pricing
  ↓
Display Pricing Plans
  ├─ Free Tier
  │   ├─ 1 verification/month
  │   ├─ Basic profile
  │   └─ Public profile
  ├─ Pro ($9.99/month)
  │   ├─ Unlimited verifications
  │   ├─ Practice mode
  │   ├─ Advanced reports
  │   └─ Priority support
  └─ Premium ($19.99/month)
      ├─ Everything in Pro
      ├─ Custom difficulty levels
      ├─ Portfolio integration
      └─ Analytics dashboard
  ↓
Candidate selects plan
  ↓
CHECK: Current subscription?
  ├─ Has active subscription → Show upgrade/downgrade options
  └─ No subscription → Continue to checkout
  ↓
Navigate to checkout
  ├─ Plan details
  ├─ Price breakdown
  └─ Payment method selection
  ↓
Payment processing
  ├─ Stripe integration
  ├─ Collect payment details
  ├─ Process payment
  └─ Create subscription
  ↓
API Call: POST /api/payments/subscribe
  ├─ Verify payment
  ├─ Create subscription record
  ├─ Update user tier
  └─ Set subscription expiry
  ↓
SUCCESS
  ├─ Show success message
  ├─ Update user tier in Redux
  ├─ Unlock premium features
  └─ Redirect to dashboard
  ↓
END
```

### 2. **Employer Subscription Flow**

```
START
  ↓
Employer views pricing page OR hits view limit
  ↓
Display Employer Pricing Plans
  ├─ Starter ($99/month)
  │   ├─ 10 candidate views/month
  │   ├─ Basic search
  │   └─ Email support
  ├─ Growth ($299/month)
  │   ├─ 100 candidate views/month
  │   ├─ Advanced search & filters
  │   ├─ Saved searches
  │   ├─ Candidate comparison
  │   └─ Priority support
  └─ Enterprise (Custom)
      ├─ Unlimited views
      ├─ API access
      ├─ ATS integration
      ├─ Dedicated support
      └─ Custom features
  ↓
Employer selects plan
  ↓
Enterprise plan selected?
  ├─ YES → Show contact form → Sales team follows up
  └─ NO → Continue to checkout
  ↓
Checkout & Payment
  ├─ Enter company details
  ├─ Payment information
  ├─ Process payment (Stripe)
  └─ Create subscription
  ↓
API Call: POST /api/payments/employer/subscribe
  ├─ Create employer subscription
  ├─ Set view limits
  ├─ Enable features based on tier
  └─ Send welcome email
  ↓
SUCCESS
  ├─ Subscription activated
  ├─ View limits reset
  ├─ Premium features unlocked
  └─ Redirect to dashboard
  ↓
END
```

### 3. **Pay-per-Verification Flow**

```
START
  ↓
Candidate wants to verify skill (free tier limit reached)
  ↓
Show payment options
  ├─ Subscribe to Pro ($9.99/month)
  ├─ Pay per verification ($4.99 one-time)
  └─ [Cancel]
  ↓
Candidate selects "Pay per verification"
  ↓
Show checkout
  ├─ Service: Skill Verification - React
  ├─ Price: $4.99
  ├─ Payment method selection
  └─ [Pay Now]
  ↓
Payment processing
  ├─ Stripe checkout
  ├─ Process payment
  └─ Verify payment success
  ↓
API Call: POST /api/payments/pay-per-verification
  ├─ Verify payment
  ├─ Grant 1 verification credit
  ├─ Record transaction
  └─ Update user credits
  ↓
SUCCESS
  ├─ Show "Payment successful!"
  ├─ Grant verification access
  ├─ Redirect to interview start
  └─ Update credits in UI
  ↓
END
```

---

## 🔔 Notification Workflows

### 1. **Interview Completion Notification Flow**

```
START
  ↓
Candidate completes interview
  ↓
Interview evaluation finished
  ↓
Score calculated and report generated
  ↓
CHECK: Score >= 70 (eligible for NFT)?
  ├─ YES → Continue
  └─ NO → Send "Interview completed, score below threshold" notification
  ↓
Create notification tasks
  ├─ In-app notification
  ├─ Email notification (if enabled)
  └─ Push notification (if enabled)
  ↓
In-app Notification
  ├─ Store in database
  ├─ Real-time push via WebSocket (if user online)
  └─ Show in notification bell icon
  ↓
Email Notification
  ├─ Generate email template
  ├─ Include:
  │   ├─ Score summary
  │   ├─ Link to view full report
  │   └─ "Mint NFT" CTA (if eligible)
  ├─ Send via email service (SendGrid/SES)
  └─ Queue for delivery
  ↓
END
```

### 2. **NFT Minting Success Notification Flow**

```
START
  ↓
NFT minted successfully on blockchain
  ↓
Transaction confirmed
  ↓
Backend processes minting event
  ↓
Create notification
  ├─ Type: "nft_minted"
  ├─ Data: { skill, tokenId, transactionHash }
  └─ Recipient: candidate
  ↓
Send notifications
  ├─ In-app: "Your React SkillProof NFT has been minted!"
  ├─ Email: "Congratulations! Your SkillProof NFT is ready"
  │   ├─ Include token ID
  │   ├─ Link to view on OpenSea
  │   └─ Share profile link
  └─ Push: "🎉 New SkillProof NFT minted"
  ↓
Update candidate dashboard
  ├─ Show new NFT badge
  └─ Refresh verification list
  ↓
END
```

### 3. **Employer Alert Notification Flow**

```
START
  ↓
Background job: Check saved searches
  ↓
Query for new candidates matching saved search criteria
  ↓
New matches found?
  ├─ NO → Skip → END
  └─ YES → Continue
  ↓
For each saved search with new matches:
  ↓
Create notification
  ├─ Type: "new_candidates_match"
  ├─ Search name: "Senior React Developers"
  ├─ New matches count: 5
  └─ Recipient: employer
  ↓
Send notifications
  ├─ Email (if daily digest enabled)
  │   ├─ Subject: "5 new candidates match your search"
  │   ├─ List of new candidates (preview)
  │   └─ Link to view all matches
  ├─ In-app notification
  │   ├─ Show in notification center
  │   └─ Badge count on "Saved Searches"
  └─ Push notification (if enabled)
  ↓
END
```

---

*This completes Part 2. The document continues with Error Handling, Complete User Journeys, State Management, Data Flow Diagrams, and Decision Trees in the final sections.*

