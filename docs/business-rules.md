# Business Rules Documentation

## AUTHENTICATION & USER MANAGEMENT

BR-01: Email must be unique across all user accounts.

BR-02: Password must be at least 6 characters long.

BR-03: Password confirmation must exactly match the new password.

BR-04: New users are created with IsActive=false and require email verification before login.

BR-05: New users are automatically assigned "buyer" role by default.

BR-06: Only users with IsActive=true can login to the system.

BR-07: Password authentication uses BCrypt hashing algorithm.

BR-08: Email cannot be changed after user registration.

BR-09: Users can only update their own profile information.

BR-10: Staff can change user roles between buyer, seller, and Staff.

BR-11: Disabled accounts have IsActive set to false and cannot login.

## PRODUCT CREATION & VALIDATION

BR-12: Product title must be between 3 and 255 characters.

BR-13: Product description must be between 10 and 2000 characters.

BR-14: Product base price must be at least 1,000 VND.

BR-15: Product category must be selected from existing categories.

BR-16: Product condition and location are required fields.

BR-17: Product status is set to "pending" when created and requires Staff approval.

BR-18: Products can only be edited when status is "pending".

BR-19: Products can only be deleted when status is "pending" or "draft".

BR-20: Rejected products cannot have auctions created.

BR-21: Rejecting a product requires a reason that cannot be empty.

## AUCTION CREATION & MANAGEMENT

BR-22: Auction can only be created for items with status "pending" or "approved".

BR-23: Auction cannot be created for rejected items.

BR-24: Seller can only create auctions for their own items.

BR-25: Item cannot have multiple active, draft, or scheduled auctions simultaneously.

BR-26: Starting bid must be greater than 0 VND.

BR-27: Buy Now price must be greater than starting bid if provided.

BR-28: Auction start time must be before end time.

BR-29: Auction start time cannot be in the past.

BR-30: Auction status is "scheduled" if start time is in future, otherwise "active".

BR-31: Item status changes to "archived" after auction is created.

BR-32: Scheduled auctions automatically become active when StartTime is reached.

BR-33: Active auctions automatically become completed when EndTime is reached.

BR-34: Completed auctions have WinnerId set to highest bidder.

BR-35: Order is automatically created for winner with status "awaiting_payment" when auction completes.

BR-36: Staff can pause active auctions, setting status to "paused" with PausedAt timestamp.

BR-37: Staff can resume paused auctions, changing status back to "active".

BR-38: Staff can cancel auctions, setting status to "cancelled".

## BIDDING RULES

BR-39: Bid can only be placed on auctions with status "active".

BR-40: Bid cannot be placed after auction end time.

BR-41: Bid amount must be greater than current bid.

BR-42: Bid amount must be at least equal to starting bid.

BR-43: Seller cannot bid on their own auctions.

BR-44: Bid increments follow a predefined tiered pricing structure based on current price.

BR-45: Bid updates are broadcast via SignalR in real-time.

BR-46: Recent bids are cached in Redis with max 100 per auction.

BR-47: System falls back to SQL database if Redis unavailable.

## AUTO-BID RULES

BR-48: Auto-bid can only be set up for active auctions.

BR-49: Auto-bid max amount must be greater than current bid + increment.

BR-50: Auto-bid automatically places bids up to maximum amount.

BR-51: Auto-bid uses latest DB value each iteration to avoid race conditions.

BR-52: Auto-bid does not trigger if user placed the manual bid.

## BUY NOW RULES

BR-53: Buy Now can only be used if BuyNowPrice is set.

BR-54: Buy Now can only be used on active auctions.

BR-55: Buy Now cannot be used after auction end time.

BR-56: Buy Now cannot be used if auction already has a winner.

BR-57: Seller cannot Buy Now on their own auction.

BR-58: Buy Now ends auction immediately and creates order.

## RATING & FEEDBACK

BR-59: Rating can only be given after auction is completed.

BR-60: Each user can only rate the other party once per auction.

## ORDER & PAYMENT RULES

BR-61: Order status flow: awaiting_payment → awaiting_shipment → shipped → completed.

BR-62: Order moves to "awaiting_shipment" when payment is received.

BR-63: Order can only be confirmed when status is "shipped".

BR-64: Confirming receipt marks order completed and releases payment.

BR-65: Reporting order issue creates dispute and changes status to "dispute".

BR-66: Shipping info can only be updated when status is "awaiting_shipment".

BR-67: Tracking number is required when updating shipping info.

BR-68: Updating shipping info sets status to "shipped" with timestamp.

BR-69: Payment status: pending → paid_held → released_to_seller/refunded_to_buyer.

BR-70: Payment changes to "hold_dispute" when dispute is created.

BR-71: Payment released to seller when buyer confirms receipt.

BR-72: Payment refunded to buyer if dispute favors buyer.

## DISPUTE RESOLUTION

BR-73: Disputes can only be created for orders.

BR-74: Dispute statuses: pending, in_review, buyer_won, seller_won, resolved, closed.

BR-75: Dispute can favor buyer (refund) or seller (release payment).

BR-76: Resolved disputes must specify refund or release.

## FAVORITE & WATCHLIST

BR-77: Users cannot add themselves to favorite sellers list.

BR-78: Seller must not already be in the user's favorite list before adding.

BR-79: Users can add auctions to watchlist.

## NOTIFICATIONS

BR-80: Notification messages max 500 characters.

BR-81: Notifications created for approval, rejection, order and payment events.

BR-82: Notifications sent asynchronously; failures do not block operations.

## INCIDENT REPORTS

BR-83: Incident reports require a reason and description.

BR-84: Staff must provide reason when approving or rejecting incident reports.

---

## Notes

- All monetary values are in Vietnamese Dong (VND)
- All timestamps use Vietnam timezone (UTC+7)
- Status transitions follow strict rules and cannot be bypassed
- Redis caching is optional - system gracefully falls back to database if Redis is unavailable

# Business Rules Documentation

## BUSINESS RULES BY USE CASE

### Registers
BR-01: Email must be unique across all user accounts.

BR-02: Password must be at least 6 characters long.

BR-03: Password confirmation must exactly match the new password.

BR-04: New users are created with IsActive=false and require email verification before login.

BR-05: New users are automatically assigned "buyer" role by default.

### View Homepage
*(No specific business rules - display only)*

### View List Auction
BR-32: Scheduled auctions automatically become active when StartTime is reached.

BR-33: Active auctions automatically become completed when EndTime is reached.

### View Product Auction Detail
BR-32: Scheduled auctions automatically become active when StartTime is reached.

BR-33: Active auctions automatically become completed when EndTime is reached.

BR-45: Bid updates are broadcast via SignalR in real-time.

### View Seller Page
*(No specific business rules - display only)*

### View All Product Of Seller
*(No specific business rules - display only)*

### Search
*(No specific business rules - search functionality)*

### Filter
*(No specific business rules - filter functionality)*

### View Rule Page
*(No specific business rules - display only)*

### Login
BR-06: Only users with IsActive=true can login to the system.

BR-07: Password authentication uses BCrypt hashing algorithm.

### Logout
*(No specific business rules - session management)*

### Change Password
BR-02: Password must be at least 6 characters long.

BR-03: Password confirmation must exactly match the new password.

BR-07: Password authentication uses BCrypt hashing algorithm.

### Join Auction
BR-39: Bid can only be placed on auctions with status "active".

BR-40: Bid cannot be placed after auction end time.

BR-43: Seller cannot bid on their own auctions.

### Place Manual Bid
BR-39: Bid can only be placed on auctions with status "active".

BR-40: Bid cannot be placed after auction end time.

BR-41: Bid amount must be greater than current bid.

BR-42: Bid amount must be at least equal to starting bid.

BR-43: Seller cannot bid on their own auctions.

BR-44: Bid increments follow a predefined tiered pricing structure based on current price.

BR-45: Bid updates are broadcast via SignalR in real-time.

BR-46: Recent bids are cached in Redis with max 100 per auction.

BR-47: System falls back to SQL database if Redis unavailable.

### Setup Auto-Bid
BR-48: Auto-bid can only be set up for active auctions.

BR-49: Auto-bid max amount must be greater than current bid + increment.

BR-50: Auto-bid automatically places bids up to maximum amount.

BR-51: Auto-bid uses latest DB value each iteration to avoid race conditions.

BR-52: Auto-bid does not trigger if user placed the manual bid.

### Buy Now
BR-53: Buy Now can only be used if BuyNowPrice is set.

BR-54: Buy Now can only be used on active auctions.

BR-55: Buy Now cannot be used after auction end time.

BR-56: Buy Now cannot be used if auction already has a winner.

BR-57: Seller cannot Buy Now on their own auction.

BR-58: Buy Now ends auction immediately and creates order.

### View Bidding History
BR-33: Active auctions automatically become completed when EndTime is reached.

BR-34: Completed auctions have WinnerId set to highest bidder.

### Feedback & Rating Seller
BR-59: Rating can only be given after auction is completed.

BR-60: Each user can only rate the other party once per auction.

### Follow Auctions
BR-79: Users can add auctions to watchlist.

### View Joined Auctions
BR-33: Active auctions automatically become completed when EndTime is reached.

### Joined Auctions Detail
BR-33: Active auctions automatically become completed when EndTime is reached.

BR-34: Completed auctions have WinnerId set to highest bidder.

### View Profile
*(No specific business rules - display only)*

### Update Profile
BR-08: Email cannot be changed after user registration.

BR-09: Users can only update their own profile information.

### Add Seller to Favorite List
BR-77: Users cannot add themselves to favorite sellers list.

BR-78: Seller must not already be in the user's favorite list before adding.

### View List Seller Follow
*(No specific business rules - display only)*

### Add Auctions Favorite
BR-79: Users can add auctions to watchlist.

### View List Auction Follow
*(No specific business rules - display only)*

### LiveChat in Auctions
BR-45: Bid updates are broadcast via SignalR in real-time.

### Delete Seller to Favorite List
*(No specific business rules - deletion operation)*

### Delete Auctions to Favorite List
*(No specific business rules - deletion operation)*

### View List Conversations
*(No specific business rules - display only)*

### View Detail Conversation
*(No specific business rules - display only)*

### Send Message
*(No specific business rules - messaging functionality)*

### View Statistics
*(No specific business rules - display only)*

### Add New Conversation
*(No specific business rules - creation operation)*

### View Notifications
BR-80: Notification messages max 500 characters.

### Mark Notifications as Read
*(No specific business rules - update operation)*

### Receive Notifications
BR-80: Notification messages max 500 characters.

BR-81: Notifications created for approval, rejection, order and payment events.

BR-82: Notifications sent asynchronously; failures do not block operations.

### AI Recommend
*(No specific business rules - recommendation algorithm)*

### Sent Incident Reports
BR-83: Incident reports require a reason and description.

### View Payment QR
BR-35: Order is automatically created for winner with status "awaiting_payment" when auction completes.

### View Payment Status
BR-69: Payment status: pending → paid_held → released_to_seller/refunded_to_buyer.

BR-70: Payment changes to "hold_dispute" when dispute is created.

BR-71: Payment released to seller when buyer confirms receipt.

BR-72: Payment refunded to buyer if dispute favors buyer.

### View Order with Shipping Info
BR-68: Updating shipping info sets status to "shipped" with timestamp.

### Confirm Order Received
BR-63: Order can only be confirmed when status is "shipped".

BR-64: Confirming receipt marks order completed and releases payment.

BR-71: Payment released to seller when buyer confirms receipt.

### Report Order Issue
BR-65: Reporting order issue creates dispute and changes status to "dispute".

BR-70: Payment changes to "hold_dispute" when dispute is created.

BR-73: Disputes can only be created for orders.

### View All Buyer Orders
BR-61: Order status flow: awaiting_payment → awaiting_shipment → shipped → completed.

### View Order Detail
BR-61: Order status flow: awaiting_payment → awaiting_shipment → shipped → completed.

BR-69: Payment status: pending → paid_held → released_to_seller/refunded_to_buyer.

### View Detail Auction Follow
BR-32: Scheduled auctions automatically become active when StartTime is reached.

BR-33: Active auctions automatically become completed when EndTime is reached.

### View Pending Products
BR-17: Product status is set to "pending" when created and requires Staff approval.

### Create New Product Auctions
BR-12: Product title must be between 3 and 255 characters.

BR-13: Product description must be between 10 and 2000 characters.

BR-14: Product base price must be at least 1,000 VND.

BR-15: Product category must be selected from existing categories.

BR-16: Product condition and location are required fields.

BR-17: Product status is set to "pending" when created and requires Staff approval.

### Delete Pending Product
BR-19: Products can only be deleted when status is "pending" or "draft".

### Save Draft Product
BR-19: Products can only be deleted when status is "pending" or "draft".

### Delete Draft Product
BR-19: Products can only be deleted when status is "pending" or "draft".

### Edit Pending Products
BR-18: Products can only be edited when status is "pending".

### View Seller's Auction Listings
BR-32: Scheduled auctions automatically become active when StartTime is reached.

BR-33: Active auctions automatically become completed when EndTime is reached.

### View Order
BR-61: Order status flow: awaiting_payment → awaiting_shipment → shipped → completed.

### Update Order Status
BR-61: Order status flow: awaiting_payment → awaiting_shipment → shipped → completed.

BR-62: Order moves to "awaiting_shipment" when payment is received.

### Add Order Information
*(No specific business rules - information addition)*

### Create Auction
BR-22: Auction can only be created for items with status "pending" or "approved".

BR-23: Auction cannot be created for rejected items.

BR-24: Seller can only create auctions for their own items.

BR-25: Item cannot have multiple active, draft, or scheduled auctions simultaneously.

BR-26: Starting bid must be greater than 0 VND.

BR-27: Buy Now price must be greater than starting bid if provided.

BR-28: Auction start time must be before end time.

BR-29: Auction start time cannot be in the past.

BR-30: Auction status is "scheduled" if start time is in future, otherwise "active".

BR-31: Item status changes to "archived" after auction is created.

### View Auctions History
BR-33: Active auctions automatically become completed when EndTime is reached.

BR-34: Completed auctions have WinnerId set to highest bidder.

### View Detail Auctions History
BR-33: Active auctions automatically become completed when EndTime is reached.

BR-34: Completed auctions have WinnerId set to highest bidder.

BR-35: Order is automatically created for winner with status "awaiting_payment" when auction completes.

### View Won Auctions
BR-33: Active auctions automatically become completed when EndTime is reached.

BR-34: Completed auctions have WinnerId set to highest bidder.

BR-35: Order is automatically created for winner with status "awaiting_payment" when auction completes.

### Feedback & Rating Buyer
BR-59: Rating can only be given after auction is completed.

BR-60: Each user can only rate the other party once per auction.

### View Payment Notification
BR-62: Order moves to "awaiting_shipment" when payment is received.

BR-81: Notifications created for approval, rejection, order and payment events.

### Update Shipping Info
BR-66: Shipping info can only be updated when status is "awaiting_shipment".

BR-67: Tracking number is required when updating shipping info.

BR-68: Updating shipping info sets status to "shipped" with timestamp.

### View Payment Release Notification
BR-71: Payment released to seller when buyer confirms receipt.

BR-81: Notifications created for approval, rejection, order and payment events.

### View Seller Buyer Orders
BR-61: Order status flow: awaiting_payment → awaiting_shipment → shipped → completed.

### Handle Dispute
BR-73: Disputes can only be created for orders.

BR-74: Dispute statuses: pending, in_review, buyer_won, seller_won, resolved, closed.

BR-75: Dispute can favor buyer (refund) or seller (release payment).

BR-76: Resolved disputes must specify refund or release.

BR-70: Payment changes to "hold_dispute" when dispute is created.

BR-72: Payment refunded to buyer if dispute favors buyer.

### Track Order Status
BR-61: Order status flow: awaiting_payment → awaiting_shipment → shipped → completed.

BR-62: Order moves to "awaiting_shipment" when payment is received.

BR-68: Updating shipping info sets status to "shipped" with timestamp.

BR-64: Confirming receipt marks order completed and releases payment.

### View Product Pending Notification
BR-17: Product status is set to "pending" when created and requires Staff approval.

BR-81: Notifications created for approval, rejection, order and payment events.

### View List Categories
*(No specific business rules - display only)*

### Create Categories
*(No specific business rules - admin operation)*

### Update Categories
*(No specific business rules - admin operation)*

### Delete Categories
*(No specific business rules - admin operation)*

### View List Pending Products
BR-17: Product status is set to "pending" when created and requires Staff approval.

### Approve Product
BR-17: Product status is set to "pending" when created and requires Staff approval.

BR-81: Notifications created for approval, rejection, order and payment events.

### Reject Product
BR-20: Rejected products cannot have auctions created.

BR-21: Rejecting a product requires a reason that cannot be empty.

BR-81: Notifications created for approval, rejection, order and payment events.

### View Detail Pending Product
BR-17: Product status is set to "pending" when created and requires Staff approval.

### Pause Auction
BR-36: Staff can pause active auctions, setting status to "paused" with PausedAt timestamp.

### Resume Auction
BR-37: Staff can resume paused auctions, changing status back to "active".

### Cancel Auction
BR-38: Staff can cancel auctions, setting status to "cancelled".

### View List Incident Reports
*(No specific business rules - display only)*

### View Detail Incident Reports
*(No specific business rules - display only)*

### Approve Incident Reports
BR-84: Staff must provide reason when approving or rejecting incident reports.

### Reject Incident Reports
BR-84: Staff must provide reason when approving or rejecting incident reports.

### Create Dispute Resolution Chat
BR-73: Disputes can only be created for orders.

BR-74: Dispute statuses: pending, in_review, buyer_won, seller_won, resolved, closed.

### Dispute Resolution
BR-73: Disputes can only be created for orders.

BR-74: Dispute statuses: pending, in_review, buyer_won, seller_won, resolved, closed.

BR-75: Dispute can favor buyer (refund) or seller (release payment).

BR-76: Resolved disputes must specify refund or release.

BR-70: Payment changes to "hold_dispute" when dispute is created.

BR-72: Payment refunded to buyer if dispute favors buyer.

### View List User Accounts
*(No specific business rules - display only)*

### Edit Info User
BR-09: Users can only update their own profile information.

### Add New User
BR-01: Email must be unique across all user accounts.

BR-02: Password must be at least 6 characters long.

BR-05: New users are automatically assigned "buyer" role by default.

### Change Role
BR-10: Staff can change user roles between buyer, seller, and Staff.

### Disable Account
BR-11: Disabled accounts have IsActive set to false and cannot login.

BR-06: Only users with IsActive=true can login to the system.

### Reactive Account
BR-11: Disabled accounts have IsActive set to false and cannot login.

BR-06: Only users with IsActive=true can login to the system.

### Manage Transactions
BR-69: Payment status: pending → paid_held → released_to_seller/refunded_to_buyer.

BR-70: Payment changes to "hold_dispute" when dispute is created.

BR-71: Payment released to seller when buyer confirms receipt.

BR-72: Payment refunded to buyer if dispute favors buyer.

---

## Notes

- All monetary values are in Vietnamese Dong (VND)
- All timestamps use Vietnam timezone (UTC+7)
- Status transitions follow strict rules and cannot be bypassed
- Notifications are sent asynchronously and failures do not block main operations
- Redis caching is optional - system gracefully falls back to database if Redis is unavailable

#	Message code	Message Type	Context	Content
1	MSG01	Toast message	Displayed when seller cannot be found	Seller not found.
2	MSG02	In red, under the text box	Shown when seller profile cannot be accessed	This seller's profile is not accessible.
3	MSG03	In red, under the text box	Displayed when user enters incorrect email or password during login	The email or password is incorrect.
4	MSG04	In red, under the text box	Shown when user tries to login but account is locked	Account Locked
5	MSG05	In red, under the text box	Displayed when current password does not match stored password during change password	The current password is incorrect.
6	MSG06	In red, under the text box	Shown when new password does not meet security requirements	The new password is too weak.
7	MSG07	Toast message	Appears when user profile information cannot be loaded	Unable to load profile data. Please try again later.
8	MSG08	In red, under the text box	Displayed when profile picture upload fails	Profile picture upload failed
9	MSG09	Toast message	Shown when no user accounts are found in admin view	No user accounts found
10	MSG10	In red, under the text box	Displayed when email is already registered during registration	Email already in use
11	MSG11	Toast message	Shown when selected role is invalid during role change	The selected role is invalid.
12	MSG12	In red, under the text box	Displayed when attempting to disable an already disabled account	This account is already disabled.
13	MSG13	In red, under the text box	Shown when attempting to reactivate an already active account	Account is already active
14	MSG14	In line	Displayed when no auctions are found in auction list	No auctions found.
15	MSG15	Toast message	Shown when user does not have permission to view auction details	You do not have permission to view this auction.
16	MSG16	In line	Displayed when seller has no products in their product list	This seller has no products
17	MSG17	In red, under the text box	Shown when Buy Now feature is not available for auction	Buy Now not available.
18	MSG18	Toast message	Displayed when seller has no pending products	You have no pending products.
19	MSG19	Toast message	Shown when attempting to delete product that is not in pending status	Only pending products can be deleted
20	MSG20	Toast message	Displayed when attempting to delete product that is not in draft status	Only draft products can be deleted
21	MSG21	Toast message	Shown when attempting to edit product that is no longer editable	This product is no longer editable.
22	MSG22	In red, under the text box	Displayed when seller has no auctions in their listings	You have no auctions.
23	MSG23	In red, under the text box	Shown when admin has no pending products for review	No pending products for review.
24	MSG24	In red, under the text box	Displayed when product has already been processed (approved/rejected)	This product has already been processed.
25	MSG25	In line	Shown when product cannot be found	Product not found.
26	MSG26	In red, under the text box	Displayed when no auctions are found in search or filter results	No auctions found
27	MSG27	In red, under the text box	Shown when attempting to pause auction that is not active	Only active auctions can be paused.
28	MSG28	In red, under the text box	Displayed when attempting to resume auction that is not paused	Only paused auctions can be resumed.
29	MSG29	In red, under the text box	Shown when cancellation reason is missing during auction cancellation	A cancellation reason is required.
30	MSG30	Toast message	Displayed when search keyword is empty	Please enter a keyword to search.
31	MSG31	In line	Shown when no items match search query	No items found for your search.
32	MSG32	In line	Displayed when no items match filter criteria	No items match your filter criteria.
33	MSG33	Toast message	Shown when rules page cannot be loaded	Rules are temporarily unavailable.
34	MSG34	In red, under the text box	Displayed when attempting to join an ended auction	Auction has ended – cannot join
35	MSG35	In red, under the text box	Shown when bid amount is lower than required minimum	Your bid is too low
36	MSG36	In red, under the text box	Displayed when attempting to place bid on ended auction	Auction has ended – cannot place bid
37	MSG37	In red, under the text box	Shown when auto-bid maximum amount is too low	Max bid is too low.
38	MSG38	In red, under the text box	Displayed when auction has no bids yet	No bids yet
39	MSG39	In red, under the text box	Shown when user has not joined any auctions	You have not joined any auctions yet.
40	MSG40	In red, under the text box	Displayed when auction is no longer available (cancelled/ended)	This auction is no longer available.
41	MSG41	In red, under the text box	Shown when seller is already in user's favorite list	Seller already in your favorites.
42	MSG42	In red, under the text box	Displayed when user has not followed any sellers	You haven't followed any sellers yet
43	MSG43	In red, under the text box	Shown when user has not followed any auctions	You haven't followed any auctions yet.
44	MSG44	In red, under the text box	Displayed when no conversations are available	No conversations available.
45	MSG45	In red, under the text box	Shown when no notifications are available	No notifications available
46	MSG46	Toast message	Displayed when all notifications are already marked as read	All notifications are already read
47	MSG47	In red, under the text box	Shown when attempting to interact with already closed dispute	Dispute already closed.
48	MSG48	In red, under the text box	Displayed when no incident reports are available	No incident reports available.
49	MSG49	Toast message	Shown when incident report cannot be found	Incident report not found.
50	MSG50	In red, under the text box	Displayed when incident report has already been processed	This report has already been processed.
51	MSG51	Toast message	Shown when dispute chat already exists for incident report	A dispute chat is already active for this report.
52	MSG52	Toast message	Displayed when payment is successfully completed	Payment successful
53	MSG53	Toast message	Shown when payment is being refunded	Paid – Refund Processing.
54	MSG54	Toast message	Displayed when no payment notifications are available	No payment notifications available.
55	MSG55	In red, under the text box	Shown when no payout release notifications are available	No payout release notifications.
56	MSG56	In red, under the text box	Displayed when no transactions are available	No transactions available
57	MSG57	In line	Shown when external payment data cannot be retrieved	Unable to retrieve external payment data.
58	MSG58	In red, under the text box	Displayed when attempting to confirm already confirmed order	This order is already confirmed
59	MSG59	In red, under the text box	Shown when no orders are available for buyer	No orders available.
60	MSG60	In line	Displayed when user does not have permission to view order	You do not have permission to view this order
61	MSG61	In red, under the text box	Shown when no orders are available for seller	No orders available.
62	MSG62	In red, under the text box	Displayed when tracking data is temporarily unavailable	Tracking data unavailable. Check later.
63	MSG63	In line	Shown when tracking number cannot be found	Tracking cannot be found for this number.
64	MSG64	In red, under the text box	Displayed when no categories are available	No categories available.
65	MSG65	In red, under the text box	Shown when category name already exists during creation	Category name already exists
66	MSG66	In red, under the text box	Displayed when attempting to delete category that is in use	Cannot delete this category because it is in use

