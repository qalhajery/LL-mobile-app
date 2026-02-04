# Ajar Landlord Mobile App - Implementation Plan

## Executive Summary

This plan outlines the implementation of the Ajar Landlord Mobile App based on comprehensive analysis of the existing Ajar codebase and product specifications.

---

## 1. Technology Decision: React Native

### Chosen: **React Native** (based on team assessment)

| Factor | React Native | Flutter | Decision Basis |
|--------|--------------|---------|----------------|
| **Language** | TypeScript | Dart | Team knows JS/TS |
| **Learning Curve** | Low | Medium | Minimize ramp-up |
| **Native Feel** | Excellent | Good | Moderately important to client |
| **Type Sharing** | Yes (reuse @ajar-online/db) | No | Leverage existing types |
| **Ecosystem** | Massive | Growing | More libraries available |

### Why React Native over Flutter:
- Team already proficient in JavaScript/TypeScript
- Can reuse TypeScript types from `@ajar-online/db` shared package
- Native components provide authentic iOS/Android feel
- Larger ecosystem for property management features (payments, maps, etc.)
- Better alignment with existing Angular team skills

### Why Not Capacitor (despite code reuse potential):
- Client preference for native-feeling experience
- Capacitor WebView has limitations on older devices
- React Native provides better long-term scalability

### Other Confirmed Decisions:
- **Customer Support:** Use existing Freshworks integration (not Zendesk)
- **KYC:** Simple image upload (not Onfido native SDK)

---

## 2. Gap Analysis

### 2.1 Missing Backend APIs (Must Build)

| Feature | Current State | Required Work |
|---------|---------------|---------------|
| FCM Token Registration | Not implemented | `POST /api/user/fcm-token` |
| Push Notification Dispatch | Not implemented | New service + Cloud Function |
| Device Management | Not implemented | `GET/DELETE /api/user/devices` |
| Freshworks Mobile | Freshworks already in use | Configure mobile widget/SDK |

### 2.2 Missing Mobile Infrastructure

| Component | Status | Notes |
|-----------|--------|-------|
| Push Notifications (FCM) | Not implemented | Spec requires GFCM integration |
| Biometric Auth | Not in spec but recommended | FaceID/TouchID for security |
| Offline Support | Not in spec | Optional but improves UX |
| Deep Linking | Not addressed | Needed for payment links, notifications |

### 2.3 Spec vs Codebase Feature Comparison

**Features in Spec NOT fully in Codebase:**
1. **Support Staff Role** - Role exists but limited implementation
2. **Passwordless Phone Login** - OTP endpoint exists, needs verification
3. **Wizard/Tutorial Screens** - Not implemented

**Features in Codebase NOT in Spec (consider for future):**
1. **Contracts Module** - Full contract management
2. **eJar Integration** - Electronic contract platform
3. **Advanced Reports** - Complex analytics
4. **Multiple Payment Providers** - MyFatoorah, Payfort, Tap (all supported)

**Confirmed Simplifications:**
1. **Customer Support** - Use existing Freshworks (not Zendesk)
2. **KYC** - Simple image upload (not Onfido native SDK)

---

## 3. Implementation Phases

### Phase 1: Foundation
**Goal:** Set up React Native infrastructure

- [ ] Initialize React Native project with TypeScript template
- [ ] Configure Expo or bare React Native (recommended: Expo for faster iteration)
- [ ] Set up Android/iOS native projects
- [ ] Install and configure `@react-native-firebase/app` and `@react-native-firebase/auth`
- [ ] Configure FCM for push notifications (`@react-native-firebase/messaging`)
- [ ] Set up environment configs (dev, staging, prod)
- [ ] Implement secure storage service (`react-native-keychain`)
- [ ] Set up CI/CD for mobile builds (TestFlight, Play Console)
- [ ] Import TypeScript types from `@ajar-online/db`
- [ ] Create shared API service layer pattern

**Reference Files (for API patterns):**
- `client/src/app/shared/auth.service.ts` - Auth flow logic
- `client/src/environments/` - Environment config patterns
- `shared/db/source/models/` - TypeScript types to reuse

---

### Phase 2: Authentication
**Goal:** Complete auth flows

- [ ] Login screen (email/password)
- [ ] Phone login with OTP (verify `POST /api/auth/send-otp-login`)
- [ ] Forgot password flow
- [ ] Reset password flow
- [ ] Logout with token cleanup
- [ ] Biometric authentication (recommended addition)
- [ ] Session management

**Reference Services (API patterns to replicate):**
- `client/src/app/shared/auth.service.ts` - Auth flow logic
- `server/src/routes/auth.ts` - Auth endpoints
- TypeScript types from `@ajar-online/db` - 100% reusable

---

### Phase 3: Tenant Panel
**Goal:** Complete tenant-facing features

**Invoices & Dashboard:**
- [ ] Tenant dashboard (simplified)
- [ ] View leased invoices list
- [ ] Invoice detail view with all fields from spec
- [ ] Invoice filtering

**Payments:**
- [ ] Payment flow integration
- [ ] Card management (list, add, remove saved cards)
- [ ] Payment processing (MyFatoorah/Payfort/Tap)
- [ ] Payment receipt view

**Maintenance & Reports:**
- [ ] Maintenance request form
- [ ] Property/Unit selection dropdowns
- [ ] Photo upload (camera + gallery integration)
- [ ] Maintenance request list and status view
- [ ] Download reports (PDF, CSV, XLSX)

**Reference Services (API patterns to replicate):**
- `client/src/app/guest-dashboard/guest.service.ts` - Tenant API patterns
- `client/src/app/dashboard/payments/payments.service.ts` - Payment flow logic
- `server/src/routes/payment.ts` - Payment endpoints

---

### Phase 4: Property Manager Core
**Goal:** Core property management features

**Dashboard & Properties:**
- [ ] Dashboard with all metrics from spec:
  - Invoice summary (Overdue, Due, Blocked, Pending Settlements)
  - Last 10 Paid Invoices
  - Actual vs Expected Collection
  - Expiring Leases
  - Monthly Rent Lost from Vacant Units
  - Unit Occupancy, Maintenance Tickets
- [ ] Quick-add buttons for all entities
- [ ] Properties CRUD with all fields
- [ ] Units CRUD with all fields
- [ ] Property/Unit filters (Name, Country, City, Area)

**Leases & Invoices:**
- [ ] Lease Management
  - Lease list with Invoice ID, Type, Amount, Status, etc.
  - Create/Edit lease with all spec fields
  - Lease invoice info management
  - Send reminder to tenant (FCM push)
  - Export lease information
- [ ] Invoice Management
  - Invoice list with all spec filters (14 filter options)
  - Create/Edit invoice
  - Archive invoice (no delete per spec)

**Expenses & Contacts:**
- [ ] Expense Management (CRUD with filters)
- [ ] Contacts Management
  - Contact roles: Tenant, Team Member, Unit Owner, Account Owner
  - Invitation methods: SMS, Email
  - Notification language preference
  - Access permissions (Read Only, Write Only, Read and Write)
  - Terminate/Reinstate contacts

**Reference Services (API patterns to replicate):**
- `client/src/app/dashboard/shared/dashboard.service.ts` - Dashboard data aggregation
- `client/src/app/dashboard/properties/property.service.ts` - Property CRUD
- `client/src/app/dashboard/leases/lease.service.ts` - Lease management
- `client/src/app/dashboard/invoices/invoice.service.ts` - Invoice operations
- `client/src/app/dashboard/expenses/expense.service.ts` - Expense tracking
- `client/src/app/dashboard/contacts/contact.service.ts` - Contact management

---

### Phase 5: Property Manager Advanced
**Goal:** KYC, Banking, Reports, Maintenance

**KYC & Settlements:**
- [ ] KYC Verification (Simple Image Upload)
  - Upload Government ID (Passport, License, National ID)
  - Image capture from camera or gallery
  - Image preview and confirmation
  - Upload progress indicator
  - KYC status tracking
  - Re-upload on rejection
- [ ] Bank Settlement
  - Settlement list with filters (Status, Bank Account, Date Range)
  - Pending vs Completed counts
  - Settlement detail view

**Bank Accounts:**
- [ ] Bank Accounts Management
  - Add bank account (Nickname, Beneficiary Name, IBAN, Currency)
  - IBAN validation (Saudi format)
  - Multiple bank accounts per user
  - Edit/Update bank account

**Files & Maintenance:**
- [ ] File Uploads (PDF, CSV, XLSX)
- [ ] Respond to Maintenance Requests
  - View maintenance request list
  - Approve/Decline requests
  - Status updates

**Reports:**
- [ ] Reports
  - 6 report types: Unit Vacancy, Expiring Leases, Unpaid Lease Invoices, Payment Update, Net Operating Income, Legal Escalation
  - Download formats (PDF, CSV, XLSX)
  - Schedule reports (Daily, Weekly, Monthly, Quarterly, Semi-Annually, Annually)

---

### Phase 6: Support Staff & Integrations
**Goal:** Support staff features and integrations

- [ ] Support Staff: Modify Bank Account Details
  - View property managers/team members list
  - Select user and view their bank accounts
  - Edit bank account details
- [ ] Customer Support Integration
  - Freshworks Mobile SDK setup (already using Freshworks)
  - Support ticket creation
  - Ticket list view
  - FAQ/Help section
- [ ] Push Notification Infrastructure
  - FCM token registration endpoint
  - Notification handlers
  - Lease expiry reminders
  - Invoice due reminders
  - Payment received notifications

---

### Phase 7: Polish & Release
**Goal:** App store ready

- [ ] Splash Screen implementation
- [ ] Wizard/Tutorial screens (Quick slides explaining how it works)
- [ ] Profile Management screen (all spec fields)
- [ ] Multilingual verification (Arabic RTL, English LTR)
- [ ] App icons and store assets
- [ ] Performance optimization
- [ ] End-to-end testing
- [ ] iOS App Store submission (TestFlight first)
- [ ] Google Play Store submission

---

## 4. Backend Changes Required

### 4.1 New Endpoints

```
POST   /api/user/fcm-token           # Register FCM device token
DELETE /api/user/fcm-token/:token    # Unregister device
GET    /api/user/devices             # List user's devices

POST   /api/notifications/send       # Admin: Send push notification
```

**Note:** Freshworks integration uses their SDK directly - no custom backend endpoints needed.

### 4.2 Database Additions

```sql
-- FCM Token Storage (Firestore or PostgreSQL)
user_device_tokens {
  user_id: string
  fcm_token: string
  device_type: 'ios' | 'android'
  device_name: string
  created_at: timestamp
  last_used_at: timestamp
}

-- Push Notification Log
push_notification_log {
  user_id: string
  account_id: number
  notification_type: string
  title: string
  body: string
  data: json
  sent_at: timestamp
  status: 'sent' | 'failed' | 'delivered'
}
```

### 4.3 Server Files to Modify

- `server/src/routes/user.ts` - Add FCM token endpoints
- `server/src/business/notification/` - Add FCM dispatch service
- `server/src/container.ts` - Register new services

---

## 5. Risks & Mitigations

### High Risk

| Risk | Impact | Mitigation |
|------|--------|------------|
| Payment provider mobile flows | WebView redirects may not work smoothly | Test all 3 providers early; implement deep linking |
| Push notification infrastructure | New development required | Set up FCM in Phase 1; allocate buffer time |
| React Native learning curve | Team knows JS but not RN specifically | Use Expo for faster iteration |

### Medium Risk

| Risk | Impact | Mitigation |
|------|--------|------------|
| Bundle size | May exceed target | Use Hermes engine; lazy load screens |
| Offline sync conflicts | Data consistency | Implement conflict resolution; server timestamp wins |
| Arabic RTL layout | UI bugs | Test every screen in Arabic early; use I18nManager |

### Low Risk

| Risk | Impact | Mitigation |
|------|--------|------------|
| i18n translation | Translation strings exist | Extract from XLIFF to JSON; use react-i18next |
| API compatibility | All APIs exist | Same auth mechanism (Firebase tokens) |

---

## 6. Future Tenant App Considerations

### Architecture for Code Sharing

```
@ajar-online/mobile-shared/     # Shared npm package
├── services/                   # Auth, Invoice, Payment, Maintenance
├── models/                     # Extend @ajar-online/db types
├── components/                 # Invoice card, Payment form, etc.
└── utils/                      # Shared helpers
```

### Feature Flag Approach

```typescript
interface AppConfig {
  appType: 'landlord' | 'tenant';
  features: {
    propertyManagement: boolean;  // landlord only
    leaseManagement: boolean;     // landlord only
    invoiceCreate: boolean;       // landlord only
    invoiceView: boolean;         // both
    makePayment: boolean;         // both
    maintenanceCreate: boolean;   // both
    maintenanceManage: boolean;   // landlord only
    reports: boolean;             // landlord only
    bankAccounts: boolean;        // landlord only
  };
}
```

### Shared Between Apps

- Authentication flows
- Invoice viewing
- Payment processing
- Maintenance request creation
- Profile management
- Push notifications
- Multilingual support

---

## 7. Verification Plan

### Testing Strategy

1. **Unit Tests:** Jest + React Native Testing Library
2. **E2E Tests:** Detox for iOS/Android automation
3. **Manual Testing:**
   - Test on minimum spec devices (Android 8+, iOS 13+)
   - Test both languages (Arabic RTL, English LTR)
   - Test all payment flows end-to-end
   - Test push notifications on real devices
   - Test offline scenarios

### Acceptance Criteria per Module

- **Auth:** Login/logout works, password reset emails received
- **Tenant:** Can view invoices, make payment, request maintenance
- **Property Manager:** All CRUD operations work, dashboard metrics accurate
- **Notifications:** Push notifications received within 30 seconds
- **i18n:** All text translated, RTL layout correct

---

## 8. Summary

| Aspect | Details |
|--------|---------|
| **Chosen Tech** | React Native with TypeScript |
| **Type Reuse** | 100% of `@ajar-online/db` TypeScript types |
| **Backend Changes** | FCM endpoints, notification service |
| **High-Risk Items** | Payment provider WebViews, Push notifications, RN learning curve |
| **Customer Support** | Freshworks (existing) |
| **KYC Approach** | Simple image upload |
| **Future-Ready** | Architecture supports tenant app code sharing |

### Immediate Actions Required

1. **Setup:** Firebase project FCM configuration
2. **Setup:** App Store / Play Store developer accounts ready
3. **Setup:** React Native development environment for team
4. **Backend:** Create FCM token registration endpoints
