# Ajar Landlord Mobile App

A React Native mobile application for property managers to manage their portfolio on-the-go.

## Overview

This app enables landlords and property managers to:
- View dashboard with key metrics and insights
- Manage properties and units
- Create and track leases
- Generate and manage invoices
- Track expenses
- Handle maintenance requests
- Manage contacts (tenants, team members)
- Process payments
- View reports

## Tech Stack

- **Framework:** React Native with TypeScript
- **State Management:** React Context + Hooks
- **Navigation:** React Navigation
- **Backend:** Firebase (Auth, Firestore, FCM)
- **API:** REST API (existing Ajar backend)
- **i18n:** react-i18next (Arabic RTL, English LTR)

## Getting Started

### Prerequisites

- Node.js 18+
- npm or yarn
- Xcode (for iOS development)
- Android Studio (for Android development)
- CocoaPods (for iOS)

### Installation

```bash
# Install dependencies
npm install

# iOS: Install pods
cd ios && pod install && cd ..

# Start Metro bundler
npm start

# Run on iOS
npm run ios

# Run on Android
npm run android
```

## Project Structure

```
src/
├── api/              # API service layer
├── components/       # Reusable UI components
├── features/         # Feature modules
│   ├── auth/
│   ├── dashboard/
│   ├── properties/
│   ├── leases/
│   ├── invoices/
│   ├── expenses/
│   ├── contacts/
│   ├── maintenance/
│   ├── reports/
│   └── settings/
├── hooks/            # Custom hooks
├── navigation/       # Navigation configuration
├── services/         # Business logic services
├── store/            # State management
├── types/            # TypeScript types
├── utils/            # Utility functions
└── i18n/             # Internationalization
```

## Related Projects

- [Ajar Web Platform](https://github.com/ajar-online/Ajar-Code) - Main web application
- Ajar Tenant App (coming soon)

## License

Proprietary - Ajar Online
