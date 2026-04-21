# ContractShield — AI Red Flag Scanner & Contract Analyzer

## Executive Summary

ContractShield is a native iOS app that empowers solo founders, freelancers, and small business owners to instantly identify dangerous clauses in contracts before signing. Unlike enterprise tools costing $10K-$50K/year, ContractShield delivers professional-grade red flag detection at $7.99/month with a privacy-first, on-device architecture.

**Key Differentiators**:
- **Price**: 1/1000th of enterprise competitors
- **Privacy**: On-device rule engine + optional local AI (no data leaves device in free tier)
- **UX**: Plain English explanations — no legal jargon
- **Platform**: Native iOS with camera scanning, not a web wrapper
- **Speed**: Instant rule-based analysis (<1s), AI-enhanced for Pro users (2-5s)

---

## Competitive Analysis

| Competitor | Price | Platform | Target User | Key Weakness |
|------------|-------|----------|-------------|--------------|
| Legal Scan | $4.99/wk - $29.99/yr | iOS | General | No rule engine, AI-only, data sent to cloud |
| LegalBoof | $4.99/wk - $99.99/yr | iOS | General | Cloud-only AI, no offline capability |
| Contract Genius | $9.99/mo | iOS | General | Tracks user data, ads in free tier |
| Contract Risk AI Scanner | iOS 18.6+ only | iOS | General | Requires iOS 18.6+, excludes most devices |
| Contract Summary Q&A | Free+IAP | iOS | General | No risk scoring, summary only |
| **ContractShield** | **$7.99/mo or $49.99/yr** | **iOS 17+** | **Small biz/freelancers** | **On-device rules + AI, offline capable, plain English** |

---

## Apple Design Guidelines Compliance

- Follow Apple HIG 2025/2026 for navigation, typography, and spacing
- Use SF Pro system font, SF Symbols for icons
- Support Dynamic Type and VoiceOver
- Dark/Light mode adaptive colors
- Use native VisionKit for document scanning (familiar UX)
- Tab-based navigation with 3 tabs: Scan, History, Settings
- No custom gesture conflicts with system gestures

---

## Technical Architecture

### Stack
- **Language**: Swift 5.9+
- **UI Framework**: SwiftUI (iOS 17+)
- **Data Storage**: SwiftData (local, iCloud optional)
- **OCR**: Apple Vision Framework (on-device)
- **Document Scanning**: VisionKit (VNDocumentCameraViewController)
- **PDF Rendering**: PDFKit
- **AI Engine**: Rule Engine (offline, free) + Coze API (cloud, Pro)
- **Charts**: Swift Charts (risk visualization)
- **IAP**: StoreKit 2
- **Minimum iOS**: 17.0

### Architecture Pattern
MVVM (Model-View-ViewModel) with clear separation:
- **Views**: SwiftUI views, one per screen/component
- **ViewModels**: @Observable classes handling business logic
- **Models**: SwiftData models for persistence
- **Services**: OCR, Rule Engine, AI Engine, Purchase Manager

---

## Module Structure & File Organization

```
ContractShield/
├── ContractShieldApp.swift          # App entry point
├── Models/
│   ├── Contract.swift               # SwiftData model for contracts
│   ├── RedFlag.swift                # Red flag data model
│   └── RiskLevel.swift              # Risk level enum
├── ViewModels/
│   ├── ScanViewModel.swift          # Scan/import logic
│   ├── AnalysisViewModel.swift      # Analysis orchestration
│   ├── HistoryViewModel.swift       # History list management
│   └── SettingsViewModel.swift      # Settings management
├── Views/
│   ├── Scan/
│   │   ├── ScanView.swift           # Main scan screen
│   │   └── DocumentScannerView.swift # VisionKit wrapper
│   ├── Analysis/
│   │   ├── AnalysisView.swift       # Analysis results screen
│   │   ├── RiskScoreView.swift      # Risk score gauge
│   │   └── RedFlagCard.swift        # Individual flag card
│   ├── History/
│   │   └── HistoryView.swift        # Scan history list
│   ├── Settings/
│   │   ├── SettingsView.swift       # Settings screen
│   │   ├── PaywallView.swift        # Pro subscription screen
│   │   └── ContactSupportView.swift # Contact support with feedback form
│   └── Components/
│       ├── RiskBadge.swift          # Risk level badge component
│       └── LoadingOverlay.swift     # Analysis loading state
├── Services/
│   ├── OCRService.swift             # Vision OCR text extraction
│   ├── RuleEngine.swift             # Keyword + regex rule engine
│   ├── CozeAIService.swift          # Coze API integration
│   └── PurchaseManager.swift        # StoreKit 2 IAP
└── Utilities/
    ├── Constants.swift              # App-wide constants & colors
    └── Extensions.swift             # Shared extensions
```

---

## Implementation Flow

### Step 1: Project Setup & Constants
- Configure Bundle ID: com.zzoutuo.ContractShield
- Set up color system, constants, extensions
- Create SwiftData models (Contract, RedFlag, RiskLevel)

### Step 2: Document Import (Scan Tab)
- Implement VisionKit document scanner wrapper
- Implement PDF file picker (UIDocumentPickerViewController)
- Implement photo library picker (PHPickerViewController)
- Create ScanView with CTA buttons and recent scans

### Step 3: OCR Text Extraction
- Implement OCRService using Vision framework
- Support image OCR and PDF text extraction
- Add legal terminology custom dictionary

### Step 4: Rule Engine (Free Tier)
- Implement 8 red flag detection rules:
  1. Unlimited personal liability
  2. Overly broad non-compete (>2 years or unlimited scope)
  3. Hidden auto-renewal terms
  4. Vague or one-sided jurisdiction
  5. Asymmetric termination rights
  6. Overreaching IP assignment
  7. Forced arbitration
  8. One-sided indemnification
- Each rule: keyword list + regex patterns + confidence scoring
- Risk level assignment: critical / warning / standard

### Step 5: AI Engine (Pro Tier)
- Implement CozeAIService for cloud-based analysis
- JSON structured output matching ContractAnalysis schema
- Fallback from AI to rule engine if network unavailable

### Step 6: Analysis Results UI
- Risk score gauge (0-100)
- Red flag cards grouped by risk level
- Plain English explanations
- Top 3 things to know summary
- Save to history action

### Step 7: History Tab
- SwiftData-backed list of past scans
- Sort by date, risk score
- Swipe to delete
- Tap to view details

### Step 8: Settings & Paywall
- Settings: About, Privacy Policy, Terms, Contact, Rate App
- Paywall: StoreKit 2 subscription management
- Free vs Pro feature gating

### Step 9: Contact Support
- Contact form with feedback backend integration
- Backend URL: https://feedback-board.iocompile67692.workers.dev
- POST /api/feedback with JSON: { name, email, subject, message, app_name }
- app_name: "ContractShield"
- Predefined subject topics (dropdown): Bug Report, Feature Request, General Feedback, Question, Other
- Fields: Subject (dropdown), Name, Email, Message, Submit button
- Network permission required (outgoing internet)
- Add to Settings view

---

## UI/UX Design Specifications

### Color System (Dark/Light Adaptive)
| Role | Light Mode | Dark Mode | Usage |
|------|-----------|-----------|-------|
| Primary | #1B2A4A | #4A6FA5 | Navigation, headers |
| Accent | #FF8C42 | #FF8C42 | CTA buttons, highlights |
| Critical | #E74C3C | #FF6B6B | Critical risk badges |
| Warning | #F39C12 | #FFD93D | Warning risk badges |
| Standard | #2ECC71 | #6BCB77 | Standard risk badges |
| Background | #FFFFFF | #0D1117 | Screen background |
| Card | #F8F9FA | #1C2333 | Card backgrounds |

### Typography
- Large Title: SF Pro Display 34pt Bold
- Title 1: SF Pro Display 28pt Bold
- Title 2: SF Pro Display 22pt Bold
- Headline: SF Pro Text 17pt Semibold
- Body: SF Pro Text 17pt Regular
- Caption: SF Pro Text 12pt Regular

### Spacing
- 8pt grid system
- Card corner radius: 12pt
- Large panel corner radius: 16pt
- CTA button corner radius: 20pt
- Standard padding: 16pt horizontal, 12pt vertical

### Tab Structure
1. **Scan** (camera.fill) — Main scan/import screen
2. **History** (clock.fill) — Past scan results
3. **Settings** (gearshape.fill) — App settings & Pro

---

## Code Generation Rules

1. Single responsibility: one feature per module
2. High cohesion, low coupling, semantic naming
3. Use @Observable (iOS 17 Observation framework), NOT ObservableObject
4. All SwiftData model attributes MUST be optional or have default values
5. All SwiftData relationships MUST have inverse relationships
6. iPad: add `.frame(maxWidth: 720).frame(maxWidth: .infinity)` to main ScrollView content
7. Use `Color.accentColor` instead of `ShapeStyle.accent`
8. No iOS 18+ only APIs — target iOS 17+
9. No comments in code unless asked
10. Merge similar code, follow Rule of Three

---

## Testing & Validation Standards

- Build must succeed with zero errors
- App must launch on iPhone 16 simulator
- All 3 tabs must be navigable
- Document scanner must present camera
- PDF import must open file picker
- Rule engine must detect at least 6/8 red flag categories
- Analysis results must display risk score and flag cards
- History must persist across app launches
- Paywall must display subscription options
- Settings must link to privacy policy and terms

---

## Build & Deployment Checklist

- [ ] Bundle ID: com.zzoutuo.ContractShield
- [ ] iOS Deployment Target: 17.0
- [ ] Camera usage description configured
- [ ] Photo library usage description configured
- [ ] Network (outgoing internet) capability configured
- [ ] Document scanner entitlement
- [ ] StoreKit 2 configuration file
- [ ] App icon configured
- [ ] Privacy policy page deployed
- [ ] Terms of use page deployed
- [ ] Support page deployed
- [ ] App Store metadata validated
- [ ] Archive and upload to App Store Connect
