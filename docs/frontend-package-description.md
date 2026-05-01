# Frontend Package Description

## Package Descriptions

| No | Package | Description |
|----|---------|-------------|
| 01 | app | Next.js App Router pages defining application routes and page components. Contains route modules for admin, auction, buyer, seller, authentication, and other features. Each sub-package represents a route/page in the application. |
| 02 | components | React component library containing reusable UI components organized by feature domain. Includes admin, auction, buyer, seller, disputes, messages components, and a comprehensive UI component library (shadcn/ui based). Provides modular and reusable UI elements for the application. |
| 03 | lib/api | API service layer providing centralized HTTP client services for backend communication. Contains service modules for authentication, auctions, admin, users, categories, payments, messages, disputes, watchlist, ratings, and more. Handles API calls, error handling, and data transformation. Separates API logic from UI components for better maintainability. |
| 04 | lib/realtime | Real-time communication module using SignalR for WebSocket connections. Contains auctionHub and messageHub for real-time updates on auctions (bids, status changes) and messaging. Manages connection lifecycle, event handling, and automatic reconnection. |
| 05 | lib/utils | Utility functions and helper methods providing common operations like formatting (currency, dates), className merging, and other shared utility functions. Supports application-wide utility needs and code reusability. |











