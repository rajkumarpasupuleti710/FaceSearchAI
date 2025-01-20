# FaceSearchAI

Register Endpoint

Endpoint
POST /register

Description
This endpoint allows a new user to register by providing their email, password, and optional name and FCM token. It validates the email format, checks for existing users, and sends a verification email if the registration is successful.

Request Payload
{
    "email": "user@example.com",
    "password": "securepassword",
    "name": "John Doe",  // Optional
    "fcm_token": "your_fcm_token"  // Optional( for app)
}
•  email: The email address of the user (required).
•  password: The password for the user account (required).
•  name: The name of the user (optional).
•  fcm_token: The Firebase Cloud Messaging token for push notifications (optional).

Response
{
    "id": 1,
    "email": "user@example.com",
    "name": "John Doe",
    "is_verified": false,
    "platform": "web"
}
•  id: The unique identifier for the user.
•  email: The email address of the user.
•  name: The name of the user.
•  is_verified: Indicates whether the user's email is verified.
•  platform: The platform from which the user registered (web or app).
Internal Logic
1. Extracts the User-Agent from the request to determine the platform (web or app).
•  Validates the email format.
3. Checks if the email already exists in the database:
•  If it exists and is not verified, it sends a verification email.
•  If it exists and is verified, it prompts the user to log in.
•  If the email is new, it creates a new user record with a hashed password and default credits.
5. Sends a verification email to the new user.


****************************************************************************************************************

Login Endpoint
Endpoint
POST /login
Description
This endpoint allows users to log in to the application using either their email and password or their UUID. Upon successful authentication, it generates and returns access and refresh tokens for app users or just an access token for web users.
Headers
•  Content-Type: application/json
Request Payload
•  email: (Optional) The email of the user for login.
•  password: (Optional) The password of the user for login.
•  uuid: (Optional) The UUID of the user for login.
•  platform: (Required) The platform type (e.g., "web" or "app").
Request Example
{
    "email": "user@example.com",  // Optional
    "password": "userpassword",    // Optional
    "uuid": null,                  // Optional
    "platform": "app"              // Required
}
Response
{
    "access_token": "your_access_token_value",
    "refresh_token": "your_refresh_token_value",  // Only for app users
    "token_type": "bearer"
}
•  access_token: The token used for authenticating subsequent requests.
•  refresh_token: The token used to obtain a new access token (only returned for app users).
•  token_type: The type of token (usually "bearer").
Internal Logic
1. Validates that either an email and password or a UUID is provided.
•  If logging in with email and password:
•  Checks if the user exists by email.
•  Verifies the provided password.
•  Checks if the user is verified.
•  Generates an access token for web users or both access and refresh tokens for app users.
•  If logging in with UUID:
•  Checks if the user exists by UUID.
•  If not found, creates a new user with the provided UUID.
•  Generates both access and refresh tokens.
4. Returns the appropriate tokens based on the login method and platform.
Error Handling
•  Returns a 404 status if the user is not registered.
•  Returns a 400 status for invalid credentials.
•  Returns a 403 status if the email is not verified.
•  Returns a 500 status for unexpected errors.
further modifications or additional endpoints documented, feel free to ask!

********************************************************************************************************


Google Sign-In Endpoint

Endpoint
POST /auth/google
Description
This endpoint handles Google Sign-In for both web and app users. It checks if the user already exists in the database and updates their information or creates a new user if they do not exist. It generates access and refresh tokens for authenticated sessions.
Request Payload
{
    "email": "user@example.com",
    "name": "John Doe",  // Optional
    "fcm_token": "your_fcm_token"  // Optional, only for app users
}
•  email: The email address of the user (required).
•  name: The name of the user (optional).
•  fcm_token: The Firebase Cloud Messaging token for push notifications (optional, only for app users).
Response
{
    "access_token": "your_access_token",
    "refresh_token": "your_refresh_token",  // Only for app users
    "token_type": "bearer"
}
•  access_token: The token used for authenticating requests.
•  refresh_token: The token used to obtain a new access token (only for app users).
•  token_type: The type of token (always "bearer").
Internal Logic
1. Extracts the User-Agent from the request to determine the platform (web or app).
•  Checks if the user already exists in the database by their email:
•  If the user exists, updates their name and FCM token (if applicable).
•  If the user does not exist, creates a new user with the provided information.
•  Marks the user as verified since Google users are pre-verified.
•  Generates access and refresh tokens for the user.
5. Returns the tokens in the response.


********************************************************************************************************

Verify Email Endpoint

Endpoint
GET /verify-email
Description
This endpoint verifies a user's email address using a token. It decodes the token to retrieve the user's email, checks if the user exists in the database, and updates their verification status. If the user is on the app platform, it generates access and refresh tokens.
Request Parameters
•  token: The verification token sent to the user's email (required).
Response
{
    "message": "Email verified successfully, push notification sent",  // Only for app users
    "access_token": "your_access_token",  // Only for app users
    "refresh_token": "your_refresh_token"  // Only for app users
}
•  message: Confirmation message indicating the email verification status.
•  access_token: The token used for authenticating requests (only for app users).
•  refresh_token: The token used to obtain a new access token (only for app users).
Internal Logic
1. Decodes the provided token to retrieve the user's email.
2. Checks if the user exists in the database:
•  If the user does not exist, raises an error.
•  If the user exists and is not verified, updates their verification status.
•  If the user is on the app platform, generates access and refresh tokens and sends a push notification.
5. Returns a confirmation message and tokens (if applicable).


********************************************************************************************************

Search Endpoint

Endpoint
POST /search
Description
This endpoint allows authenticated users to perform a search using either an image URL or an uploaded image file. It checks the user's available credits and processes the search accordingly.
Headers
•  Authorization: Bearer token for authentication (e.g., Authorization: Bearer <access_token>)
Request Payload
•  url: (Optional) The URL of the image to search.
•  file: (Optional) The image file to upload for the search.
Request Example
{
    "url": "http://example.com/image.jpg",  // Optional
    "file": null  // Optional
}
Response
{
    "search_id": 1,
    "status": "success",
    "user_email": "user@example.com",
    "is_premium": false,
    "remaining_free_credits": 2,
    "remaining_paid_credits": 0,
    "data": [
        // Array of search results
    ]
}
•  search_id: The unique identifier for the search.
•  status: The status of the search operation (success/error).
•  user_email: The email of the user who performed the search.
•  is_premium: Indicates whether the user has a premium status.
•  remaining_free_credits: The number of free credits remaining for the user.
•  remaining_paid_credits: The number of premium credits remaining for the user.
•  data: The search results returned from the processing.
Internal Logic
•  Validates that either a URL or a file is provided.
2. Checks if the user has available credits before processing the search.
•  Determines the image source type (URL or file).
•  Validates the file type if a file is uploaded.
•  Processes the search using the appropriate method based on the user's premium status.
•  Deducts credits after a successful search.
•  Saves the search history in the database.
•  Returns the search results along with user credit information.


********************************************************************************************************

Public Search Endpoint

Endpoint
POST /public-search
Description
This endpoint allows non-registered users to perform a search using either an image URL or an uploaded image file. It implements a daily credit system, allowing 8 free searches per IP address.
Request Payload
•  url: (Optional) The URL of the image to search.
•  file: (Optional) The image file to upload for the search.
Request Example
{
    "url": "http://example.com/image.jpg",  // Optional
    "file": null  // Optional
}
Response
{
    "status": "success",
    "data": [
        // Array of search results
    ],
    "credits_info": {
        "ip_address": "192.168.1.1",
        "credits_remaining": 7,
        "daily_limit": 8,
        "next_reset": "2024-01-21T10:00:00Z"
    }
}
•  status: The status of the search operation (success/error).
•  data: The search results returned from the processing.
•  credits_info: Information about the user's credit status.
•  ip_address: The IP address of the user.
•  credits_remaining: The number of free credits remaining for the user.
•  daily_limit: The daily limit of searches allowed.
•  next_reset: The timestamp for when the credits will reset.
Internal Logic
•  Verifies the API key for security.
2. Checks if the user already exists based on their IP address.
•  Creates a new public user record if it does not exist.
•  Resets the user's credits if 24 hours have passed since the last reset.
•  Validates that either a URL or a file is provided.
6. Processes the search using the appropriate method.
7. Deducts credits after a successful search. And   Returns the search results along with credit information.
7. Get History Endpoint
Endpoint
GET /history
Description
This endpoint retrieves the search history for the currently authenticated user. It allows pagination through the use of skip and limit parameters.

Headers
•  Authorization: Bearer token for authentication (e.g., Authorization: Bearer <access_token>)

Request Parameters
•  skip: (Optional) The number of records to skip (default is 0).
•  limit: (Optional) The maximum number of records to return (default is 10).
Request Example
GET /history?skip=0&limit=10
Response
[
    {
        "id": 1,
        "query": "http://example.com/image.jpg",
        "result_count": 5,
        "timestamp": "2024-01-20T10:00:00Z",
        "platform": "web",
        "search_results": [
            // Array of search results
        ]
    },
    // More entries...
]
•  id: The unique identifier for the search history entry.
•  query: The search query used.
•  result_count: The number of results returned for the search.
•  timestamp: The timestamp of when the search was performed.
•  platform: The platform from which the search was made (web/app).
•  search_results: The actual search results returned from the processing.
Internal Logic
1. Retrieves the search history for the current authenticated user from the database.
•  Maps the query results to the SearchHistoryResponse model.
3. Handles potential JSON decoding errors gracefully.
4. Returns the search history entries.



********************************************************************************************************



Get History Preview Endpoint

Endpoint
GET /history-preview
Description
This endpoint retrieves a preview of the search history for the currently authenticated user, returning only the first search result for each entry. It also supports pagination through skip and limit parameters.
Headers
•  Authorization: Bearer token for authentication (e.g., Authorization: Bearer <access_token>)
	
Request Parameters
•  skip: (Optional) The number of records to skip (default is 0).
•  limit: (Optional) The maximum number of records to return (default is 10).
Request Example
GET /history-preview?skip=0&limit=10
Response
[
    {
        "id": 1,
        "query": "http://example.com/image.jpg",
        "result_count": 5,
        "timestamp": "2024-01-20T10:00:00Z",
        "platform": "web",
        "search_results": [
            // Array containing only the first search result
        ]
    },
    // More entries...
]
•  id: The unique identifier for the search history entry.
•  query: The search query used.
•  result_count: The total number of results returned for the search.
•  timestamp: The timestamp of when the search was performed.
•  platform: The platform from which the search was made (web/app).
•  search_results: An array containing only the first search result.
Internal Logic
1. Retrieves all history entries for the current authenticated user from the database.
2. Processes each entry to include only the first search result.
•  Handles potential JSON decoding errors and index errors gracefully.
4. Returns the search history preview entries.



********************************************************************************************************



Find Info Endpoint

Endpoint
POST /find_info
Description
This endpoint allows authenticated users with premium status to retrieve additional information based on a specific search history entry. It processes the source URLs and generates a GPT analysis based on the scraped data.
Headers
•  Authorization: Bearer token for authentication (e.g., Authorization: Bearer <access_token>)

Request Payload
{
    "uuid": "123e4567-e89b-12d3-a456-426614174000",  // The UUID of the search history entry
    "sources": [
        {
            "source_url": "http://example.com",
            "source_image_url": "http://example.com/image.jpg"
        }
    ]
}
•  uuid: The unique identifier of the search history entry (required).
•  sources: An array of source objects containing URLs and image URLs (required).
Response
{
    "message": "GPT result updated successfully",
    "search_history_id": 1,
    "gpt_result": {
        // GPT analysis result
    },
    "search_results": [
        // Array of search results
    ]
}
•  message: Confirmation message indicating the result of the operation.
•  search_history_id: The ID of the search history entry.
•  gpt_result: The result of the GPT analysis.
•  search_results: The original search results associated with the entry.
Internal Logic
1. Checks if the current user has premium status.
•  Fetches the search history entry based on the provided UUID.
•  Processes the source URLs and images.
4. Scrapes data from the provided sources.
•  Generates a GPT analysis based on the scraped data.
6. Updates the search history entry with the GPT result.
7. Returns a confirmation message along with the GPT result and original search results.
---

********************************************************************************************************

Profile Endpoint

Endpoint
Headers
•  Authorization: Bearer token for authentication (e.g., Authorization: Bearer <access_token>)

GET /profile
Description
This endpoint retrieves the profile information of the currently authenticated user, including their subscription details, credit status, and search history count.
Request Example
GET /profile
Response
{
    "id": 1,
    "name": "John Doe",
    "email": "user@example.com",
    "phone": "123-456-7890",
    "profile_url": "http://example.com/profile.jpg",
    "platform": "web",
    "is_verified": true,
    "is_active": true,
    "is_locked": false,
    "locked_at": null,
    "failed_attempts": 0,
    "gsheet_link": "http://example.com/sheet",
    "fcm_token": "your_fcm_token",
    "subscription": {
        "plan_id": "premium",
        "premium_status": true,
        "expiry_date": "2024-12-31 23:59:59",
        "last_payment_date": "2024-01-01 12:00:00",
        "purchase_token": "purchase_token_value",
        "subscription_status": "active"
    },
    "credits": {
        "total": 10,
        "used": 5,
        "remaining": 5,
        "free": 3
    },
    "total_searches": 20,
    "feedback": null
}
•  id: The unique identifier for the user.
•  name: The name of the user.
•  email: The email address of the user.
•  phone: The phone number of the user.
•  profile_url: The URL to the user's profile picture.
•  platform: The platform from which the user is accessing (web/app).
•  is_verified: Indicates whether the user's email is verified.
•  is_active: Indicates whether the user's account is active.
•  is_locked: Indicates whether the user's account is locked.
•  locked_at: Timestamp of when the account was locked (if applicable).
•  failed_attempts: Number of failed login attempts.
•  gsheet_link: Link to the user's Google Sheet (if applicable).
•  fcm_token: The Firebase Cloud Messaging token for push notifications.
•  subscription: Object containing subscription details.
•  credits: Object containing credit information.
•  total_searches: The total number of searches performed by the user.
•  feedback: Any feedback provided by the user.
Internal Logic
1. Retrieves the user profile information from the database.
2. Calculates the number of searches performed by the user.
•  Calculates the remaining credits based on the user's subscription and usage.
4. Returns the user's profile information along with subscription and credit details.



********************************************************************************************************


Apple Auth Endpoint

Endpoint
POST /auth/apple
Description
This endpoint handles user authentication via Apple Sign-In. It creates or updates the user in the database and generates access and refresh tokens for authenticated sessions.
Request Payload
{
    "email": "user@example.com",
    "name": "John Doe",  // Optional
    "fcm_token": "your_fcm_token"  // Optional( for app)
}
•  email: The email address of the user (required).
•  name: The name of the user (optional).
•  fcm_token: The Firebase Cloud Messaging token for push notifications (optional).
Response
{
    "access_token": "your_access_token",
    "refresh_token": "your_refresh_token",
    "token_type": "bearer"
}
•  access_token: The token used for authenticating requests.
•  refresh_token: The token used to obtain a new access token.
•  token_type: The type of token (always "bearer").
Internal Logic
1. Attempts to create or update the user based on the provided Apple Sign-In data.
•  Generates access and refresh tokens for the user.
•  Sends a welcome notification if an FCM token is provided.
•  Returns the tokens in the response.



********************************************************************************************************



Generate PDF Endpoint

Endpoint
POST /pdf/generate
Description
This endpoint generates a PDF report based on the user's search history and provided data. It requires the user to have premium status and processes the search results to create the PDF.
Headers
•  Authorization: Bearer token for authentication (e.g., Authorization: Bearer <access_token>)
	
Request Payload
{
    "uuid": "123e4567-e89b-12d3-a456-426614174000",  // The UUID of the search history entry
    "data": {
        // Optional data for PDF generation
    },
    "bg_image_path": "path/to/background/image.jpg"  // Optional background image path
}
•  uuid: The unique identifier of the search history entry (required).
•  data: Optional data to include in the PDF.
•  bg_image_path: Optional path to a background image for the PDF.
Response
•  Content-Disposition: attachment; filename="generated_report.pdf"
•  Content-Length: Size of the generated PDF file.
•  media_type: application/pdf
Internal Logic
•  Checks if the current user has premium status.
2. Fetches the search history entry based on the provided UUID.
3. Parses the search results and ensures they are in the correct format.
4. Prepares data for PDF generation, including the GPT result if available.
5. Validates the existence of the background image.
6. Generates the PDF using the provided data and background image.
7. Returns the generated PDF as a streaming response, with appropriate headers for file download.
•  Cleans up temporary files after the response is sent.



********************************************************************************************************


Get Google Sheet Link Endpoint

Endpoint
GET /gsheet/link
Description
This endpoint retrieves the Google Sheet link associated with the currently authenticated user.
Headers
•  Authorization: Bearer token for authentication (e.g., Authorization: Bearer <access_token>)

Request Example
GET /gsheet/link
Response
{
    "gsheet_link": "http://example.com/your_google_sheet_link"
}
•  gsheet_link: The Google Sheet link for the user.
Internal Logic
•  Fetches the user data from the database.
2. Checks if the user has a Google Sheet link.
3. Returns the Google Sheet link if it exists, otherwise raises an error.


********************************************************************************************************


Initialize Subscription Endpoint

Endpoint
POST /subscriptions/initialize
Description This endpoint initializes a subscription by creating a Stripe checkout session for the selected plan. It handles both registered and anonymous users, setting up the appropriate session parameters based on the user's status.
Request Payload
Headers (optional can handle anonymous users and registered users)
•  Authorization: Bearer token for authentication (e.g., Authorization: Bearer <access_token>)

json
{
    "plan_id": “basic_plan”
}
•	plan_id: The unique identifier for the selected subscription plan (required).
Response
json
{
    "status": "success",
    "checkout_url": "https://checkout.stripe.com/c/pay/cs_test_a19CJRINOFyGBch3qx5T7fdmhgRDwFElXdeIMaxpMUi9oMH3Cmhf9r89Re",
    "session_id": "cs_test_a19CJRINOFyGBch3qx5T7fdmhgRDwFElXdeIMaxpMUi9oMH3Cmhf9r89Re"
}
•	status: The status of the initialization operation.
•	checkout_url: The URL for the Stripe checkout session.
•	session_id: The unique identifier for the checkout session.
Internal Logic
1.	Generates a unique session ID.
2.	Fetches the selected plan from the database using the plan_id.
o	If the plan is not found, raises a "Plan not found" error.
3.	Sets up the base session parameters for Stripe, including payment method types, line items, success and cancel URLs, and metadata.
4.	Checks if the current user is authenticated:
o	If the user is authenticated (registered user), uses the user's email and sets up metadata with the user ID.
o	If the user is not authenticated (anonymous user), sets up billing address collection and payment method collection.
5.	Creates a Stripe checkout session with the specified parameters.
6.	Logs the checkout session details for debugging purposes.
7.	Returns the checkout URL and session ID in the response.


********************************************************************************************************


Stripe Webhook Endpoint

Endpoint
POST /webhooks/stripe
Description This endpoint handles Stripe webhook events by validating the webhook signature and processing different event types, such as checkout.session.completed, invoice.payment_succeeded, customer.subscription.deleted, and invoice.payment_failed.
Request Payload The payload is automatically sent by Stripe when a webhook event is triggered. It includes the event type and data.
Response
json
{
    "status": "success",
    "message": "Successfully processed {event.type} event"
}
•	status: The status of the webhook processing.
•	message: A message indicating the successful processing of the event type.
Internal Logic
1.	Extracts the webhook payload and signature header from the request.
2.	Verifies the webhook signature using the Stripe secret.
3.	Logs the received webhook event details for debugging purposes.
4.	Processes different event types:
o	checkout.session.completed: Calls handle_checkout_completed.
o	invoice.payment_succeeded: Calls handle_payment_succeeded.
o	customer.subscription.deleted: Calls handle_subscription_deleted.
o	invoice.payment_failed: Calls handle_payment_failed.
5.	If the event type is successfully processed, returns a success response.
6.	If there is an error during webhook verification or processing, logs the error and raises an HTTPException.


********************************************************************************************************


Endpoint
GET /payment/success

Description
This endpoint handles the success page for payment transactions. It retrieves the checkout session details, determines if the user is registered or anonymous, and redirects to the appropriate success page.

Request Payload
The session ID is passed as a query parameter:

plaintext
/payment/success?session_id=YourSessionID
Response
The endpoint redirects to one of the following URLs based on the user type:
•	For registered users: Redirects to the success page without tokens.
•	For anonymous users: Redirects to the reset-password page with an access token.
Internal Logic
1.	Retrieve the checkout session:
o	Uses the session ID to retrieve the checkout session from Stripe.
2.	Check if the user is registered:
o	If the user is registered, logs the user details and subscription ID.
o	Redirects to the success page without tokens.
3.	Handle anonymous users:
o	Retrieves the customer email and subscription details from the session.
o	Finds the user by email in the database.
o	Generates an access token for the anonymous user.
o	Logs the user details and subscription ID.
o	Redirects to the reset-password page with the access token.
4.	Handle Stripe errors:
o	Logs the Stripe error details and redirects to an error page with the error message.
5.	Handle general errors:
o	Logs the error details and redirects to an error page with a generic error message.
Example Response URLs
•	For registered users: https://face-search.vercel.app/subscription/success?status=success&email=YourEmail
•	For anonymous users: https://face-search.vercel.app/reset-password?access_token=YourAccessToken&email=YourEmail



********************************************************************************************************


Endpoint
GET /payment/canceled

Description
This endpoint handles the cancellation of payment transactions. It logs the cancellation attempt and redirects to the appropriate page with a cancellation message.

Request Payload
The session ID is passed as a query parameter (optional):

plaintext
/payment/canceled?session_id=YourSessionID
Response
The endpoint redirects to one of the following URLs:
•	Success URL: Redirects to the canceled page with a cancellation message.
•	Error URL: Redirects to the canceled page with a generic error message.
Internal Logic
1.	Log the cancellation attempt:
o	Logs the session ID and timestamp.
2.	Redirect to the canceled page:
o	Constructs the URL with a cancellation message and status.
o	Redirects to the constructed URL.
3.	Handle errors:
o	Logs the error details and redirects to an error page with a generic error message.
Example Response URLs
•	Success URL: https://face-search.vercel.app/payment/canceled?message=Payment%20was%20canceled&status=canceled
•	Error URL: https://face-search.vercel.app/payment/canceled?message=Something%20went%20wrong&status=error


********************************************************************************************************


Verify App Subscription Endpoint

Endpoint
POST /subscriptions/verify/app
Description
This endpoint verifies a subscription for users who signed up via an app. It checks the subscription receipt against Google Play and updates the user's subscription status in the database.
Request Payload
{
    "purchaseToken": "your_purchase_token",
    "packageName": "com.example.app",
    "subscriptionId": "your_subscription_id",
    "access_token": "your_access_token"
}
•  purchaseToken: The token received from the purchase (required).
•  packageName: The package name of the app (required).
•  subscriptionId: The ID of the subscription (required).
•  access_token: The access token for the user (required).
Response
{
    "message": "Subscription verified and updated successfully (Payment received)",
    "subscription_expiry": "2024-12-31T23:59:59Z",
    "premium_status": true,
    "credits": 50,
    "subscription_status": "Active",
    "auto_renewing": true
}
•  message: Confirmation message indicating the result of the operation.
•  subscription_expiry: The expiry date of the subscription.
•  premium_status: Indicates whether the user has premium status.
•  credits: The number of credits available to the user.
•  subscription_status: The current status of the subscription.
•  auto_renewing: Indicates whether the subscription is set to auto-renew.
Internal Logic
1. Decodes the access token to retrieve the user ID.
•  Validates the subscription receipt with Google Play.
•  Updates the user's subscription details in the database based on the subscription information received.
4. Returns a confirmation message along with updated subscription details.



********************************************************************************************************


Google Play Notifications Endpoint

Endpoint
POST /notifications/google-play
Description
This endpoint handles notifications sent from Google Play regarding subscription events such as purchases, renewals, cancellations, and expirations.
Request Payload
{
    "message": {
        "data": "base64_encoded_notification_data"
    }
}
•  data: Base64 encoded notification data from Google Play (required).
Response
{
    "status": "success",
    "message": "Successfully processed notification type SUBSCRIPTION_PURCHASED",
    "notification_type": "SUBSCRIPTION_PURCHASED"
}
•  status: The status of the notification processing (success/error).
•  message: Confirmation message indicating the result of the operation.
•  notification_type: The type of notification received.
Internal Logic
•  Decodes the base64 data from the notification.
•  Extracts the notification details, including the subscription notification type.
•  Processes the notification based on its type (e.g., subscription purchased, canceled, renewed, or expired).
•  Updates the user's subscription status in the database accordingly.
•  Returns a confirmation message indicating the result of the processing.


********************************************************************************************************


Submit Feedback Endpoint

Endpoint
POST /feedback
Description
This endpoint allows authenticated users to submit feedback, including a rating and optional content. It validates the rating and content before saving it to the database.
Headers
•  Authorization: Bearer token for authentication (e.g., Authorization: Bearer <access_token>)

Request Payload
{
    "feedback": {
        "rating": 5,  // Rating must be an integer between 1 and 5
        "content": "Great service!"  // Optional feedback content
    }
}
•  rating: The rating given by the user (required, must be an integer between 1 and 5).
•  content: Optional text feedback provided by the user (maximum 500 characters).
Response
{
    "message": "Feedback submitted successfully",
    "status": "success",
    "feedback": {
        "user_name": "John Doe",
        "rating": 5,
        "content": "Great service!",
        "submitted_at": "2024-01-20T10:00:00Z"
    }
}
•  message: Confirmation message indicating the result of the operation.
•  status: The status of the feedback submission (success/error).
•  feedback: Object containing the submitted feedback details.
Internal Logic
•  Validates the rating to ensure it is an integer between 1 and 5.
2. Validates the content to ensure it does not exceed 500 characters and performs basic sanitization.
3. Updates the user's feedback in the database.
4. Returns a confirmation message along with the submitted feedback details.


********************************************************************************************************


Get All Feedback Endpoint
Endpoint
GET /feedback/all
Description
This endpoint retrieves all user feedback submitted to the system. It returns a list of feedback entries along with the user names and submission timestamps.

Headers
•  Authorization: Bearer token for authentication (e.g., Authorization: Bearer <access_token>)

Request Example
GET /feedback/all
Response
{
    "status": "success",
    "feedback_count": 10,
    "feedback": [
        {
            "user_name": "John Doe",
            "rating": 5,
            "content": "Great service!",
            "submitted_at": "2024-01-20T10:00:00Z"
        },
        // More feedback entries...
    ]
}
•  status: The status of the feedback retrieval (success/error).
•  feedback_count: The total number of feedback entries retrieved.
•  feedback: An array of feedback objects, each containing:
•  user_name: The name of the user who submitted the feedback.
•  rating: The rating given by the user.
•  content: The feedback content provided by the user.
•  submitted_at: The timestamp of when the feedback was submitted.
Internal Logic
•  Queries the database for users with non-null feedback.
2. Collects and formats the feedback entries into a list.
•  Returns the list of feedback entries along with the total count.


********************************************************************************************************


Get History Detail Endpoint
Endpoint
GET /history-detail/{history_id}
Description This endpoint retrieves detailed search history for a specific ID. It checks if the user is authenticated and fetches the search history associated with the given history ID for the current user.
Headers
•  Authorization: Bearer token for authentication (e.g., Authorization: Bearer <access_token>)

Request Parameters
•	history_id: The unique identifier for the search history (required).
Response
json
{
    "id": "history_id",
    "query": "search_query",
    "timestamp": "timestamp",
    "platform": "web",
    "gpt_result": null,
    "search_results": []
}
•	id: The unique identifier for the search history.
•	query: The search query made by the user.
•	timestamp: The timestamp when the search was made.
•	platform: The platform from which the search was made (web or app).
•	gpt_result: The GPT-3/4 generated result for the search (if any).
•	search_results: The search results returned for the query.
Error Responses
json
{
    "status": "error",
    "message": "User must be logged in to perform this action.",
    "error_code": "USER_NOT_AUTHENTICATED"
}
•	status: The status of the response, indicating an error.
•	message: A message describing the error.
•	error_code: The error code, indicating the user is not authenticated.
json
{
    "detail": "History not found"
}
•	detail: A message indicating the search history was not found.
json
{
    "detail": "Failed to fetch history detail"
}
•	detail: A message indicating a failure in fetching the search history detail due to an internal server error.
Internal Logic
1.	Checks if the current user is authenticated:
o	If not, returns an error response indicating the user must be logged in.
2.	Fetches the search history associated with the given history ID and the current user's ID.
3.	If the history is not found, raises a "History not found" error.
4.	Parses the search results and GPT results if they exist.
5.	Returns the search history details in the response.



********************************************************************************************************


Get Plans Endpoint
Endpoint
GET /plans
Description This endpoint retrieves all available subscription plans from the database.

Response
json
[
    {
        "plan_id": 1,
        "name": "Basic Plan",
        "description": "Access to basic features",
        "price": 9.99,
        "duration": 30,
        "credits": 100,
        "features": ["Feature 1", "Feature 2"]
    },
    {
        "plan_id": 2,
        "name": "Premium Plan",
        "description": "Access to premium features",
        "price": 19.99,
        "duration": 30,
        "credits": 200,
        "features": ["Feature 1", "Feature 2", "Feature 3"]
    }
]
•	plan_id: The unique identifier for the subscription plan.
•	name: The name of the subscription plan.
•	description: A brief description of the plan.
•	price: The price of the subscription plan.
•	duration: The duration of the subscription in days.
•	credits: The number of credits included in the plan.
•	features: A list of features included in the plan.
Internal Logic
1.	Queries all available subscription plans from the database.
2.	Checks if any plans are found:
o	If no plans are found, raises a "No plans found" error.
3.	Transforms the database result to match the Pydantic model PlanResponse.
4.	Returns the list of subscription plans in the response.



********************************************************************************************************


RevenueCat Webhook Endpoint
Endpoint
POST /webhooks/revenuecat
Description This endpoint handles RevenueCat webhook events by validating the request payload, extracting user details, and updating or creating user records based on the subscription events received.
Headers       (optional , can handle anonymous user(first visit) and registered user)
•  Authorization: Bearer token for authentication (e.g., Authorization: Bearer <access_token>)  

Request Payload The payload is automatically sent by RevenueCat when a webhook event is triggered. It includes the event type and user details.
Response
json
{
    "status": "success",
    "message": "Successfully processed {event_type}",
    "details": {
        "event_type": "INITIAL_PURCHASE",
        "user_id": 1,
        "uuid": "app_user_id",
        "plan_name": "Premium Plan",
        "credits": 100,
        "subscription_status": "ACTIVE",
        "access_token": "new_access_token",
        "refresh_token": "new_refresh_token",
        "processed_at": "2025-01-16T10:32:02Z"
    }
}
•	status: The status of the webhook processing.
•	message: A message indicating the successful processing of the event type.
•	details: Detailed information about the event, user, subscription plan, tokens, and processing time.
Internal Logic
1.	Validates the request payload:
o	If the payload is invalid, raises an error.
2.	Extracts user details from the payload, including app_user_id, original_app_user_id, and subscriber_attributes.
3.	Finds the user by email first:
o	If not found, checks by UUID (app_user_id or original_app_user_id).
o	If still not found, creates a new user record with the provided details.
o	If found, updates existing user details if necessary.
4.	Generates access and refresh tokens based on the UUID.
5.	Processes subscription events:
o	INITIAL_PURCHASE: Activates the subscription and updates the user's credits and status.
o	RENEWAL: Renews the subscription and updates the user's credits and status.
o	CANCELLATION: Cancels the subscription and updates the user's status.
o	EXPIRATION: Expires the subscription and updates the user's credits and status.
6.	Updates the database with the subscription details and commits the changes.
7.	Returns a response with the tokens and other details.



********************************************************************************************************


Endpoint
POST /reset-password
Description
This endpoint handles password reset requests by validating the request payload, updating the user's password, and returning a success message.

Headers
•  Authorization: Bearer token for authentication (e.g., Authorization: Bearer <access_token>)

Request Payload
The payload should include the new password in the following format:
json
{	
    "new_password": "YourNewPassword"
}
Response
json
{
    "status": "success",
    "message": "Password has been reset successfully.",
    "details": {
        "user_id": 1,
        "new_password": "YourNewPassword",
        "processed_at": "2025-01-17T13:18:02Z"
    }
}
•	status: The status of the password reset request.
•	message: A message indicating the successful processing of the request.
•	details: Detailed information about the user and the processing time.
Internal Logic
1.	Validates the request payload:
o	If the payload is invalid, raises an error.
2.	Checks if the user is logged in:
o	If not logged in, raises an HTTPException with status code 401 and a detail message.
3.	Logs user ID and request body:
o	Logs the user ID and the new password.
4.	Updates the password:
o	Hashes the new password and updates the user's hashed_password field in the database.
o	Commits the changes to the database.
5.	Returns a response:
o	Returns a JSON response with the status, message, and details.


********************************************************************************************************


Endpoint
POST /refresh-token
Description
This endpoint handles token refresh requests by validating the refresh token, finding the associated user, and generating new access and refresh tokens.

Request Payload
The payload should include the refresh token in the following format:
json
{
    "refresh_token": "YourRefreshToken"
}
Response
json
{
    "status": "success",
    "message": "Token has been refreshed successfully.",
    "details": {
        "access_token": "YourNewAccessToken",
        "refresh_token": "YourNewRefreshToken",
        "token_type": "bearer",
        "user_id": 1,
        "processed_at": "2025-01-17T13:18:02Z"
    }
}
•	status: The status of the token refresh request.
•	message: A message indicating the successful processing of the request.
•	details: Detailed information about the new tokens, user, and processing time.
Internal Logic
1.	Validates the refresh token:
o	Decodes the refresh token and extracts the user identifier (sub).
o	If the token is invalid or expired, raises an HTTPException with status code 401 and an appropriate error message.
2.	Finds the user:
o	Tries to find the user by UUID, email, or ID based on the user identifier.
o	If the user is not found, raises an HTTPException with status code 404 and a "User not found" message.
3.	Generates new tokens:
o	Creates a new access token and refresh token for the user.
4.	Returns a response:
o	Returns a JSON response with the status, message, and details.


********************************************************************************************************


Forgot Password
Endpoint
/forgot-password
**POST** /forgot-password
- **Description**: Initiates the password reset process by sending a password reset link to the user's email.

- **Request Payload**: 
  {
    "email": "user@example.com"
  }
- **Response**: 
  {
    "message": "If an account exists with this email, you will receive a password reset link.",
    "email": "user@example.com",
    "reset_token": "generated_reset_token"
  }
- **Error Codes**:
  - 500: Failed to process password reset request

## Example Request
curl -X POST "http://your-api-url/forgot-password" -H "Content-Type: application/json" -d '{"email": "user@example.com"}'

## Example Response
{
  "message": "If an account exists with this email, you will receive a password reset link.",
  "email": "user@example.com",
  "reset_token": "generated_reset_token"
}

## Notes
- The reset token is valid for 1 hour.
- The user will receive an email with a password reset link if an account exists with the provided email.


********************************************************************************************************



Middleware: security_middleware
Description This module implements middleware for a FastAPI application that handles API key validation, token blacklisting, and security headers. It also includes an error handler for HTTP exceptions related to API key and authorization issues. Additionally, it initializes a Redis client for managing token states.
Function Signature
python
@app.middleware("http")
async def security_middleware(request: Request, call_next):
Parameters
•	request: The incoming HTTP request object.
•	call_next: A function that takes the request as a parameter and returns the response.
Behavior
1.	CORS Preflight Handling:
o	The middleware skips processing for OPTIONS requests, which are typically used for CORS preflight checks.
2.	Token Validation:
o	It checks for the Authorization header in the request.
o	If the header is present and starts with "Bearer ", it extracts the token.
o	The token is checked against a Redis database to determine if it is blacklisted.
o	If the token is found to be blacklisted, a 401 Unauthorized response is returned with an error message.
3.	Security Headers:
o	The middleware adds several security headers to the response:
	X-Content-Type-Options: Prevents MIME type sniffing.
	X-Frame-Options: Protects against clickjacking by preventing the page from being displayed in a frame.
	X-XSS-Protection: Enables cross-site scripting (XSS) protection.
	Strict-Transport-Security: Enforces secure (HTTPS) connections (only added in production).
Return Value
•	Returns the response object after processing the request.
Error Handler: api_error_handler
Description The api_error_handler function handles HTTP exceptions raised during request processing, specifically for API key and authorization errors.
Function Signature
python
@app.exception_handler(HTTPException)
async def api_error_handler(request: Request, exc: HTTPException):
Parameters
•	request: The incoming HTTP request object.
•	exc: The HTTPException that was raised.
Behavior
•	If the exception's status code is 401 Unauthorized or 403 Forbidden, it returns a JSON response with an error message and an appropriate error code:
o	API_KEY_ERROR for API key-related issues.
o	AUTHORIZATION_ERROR for general authorization failures.
•	For other HTTP exceptions, it returns a JSON response.
with the status code and detail of the exception.
Return Value
•  Returns a JSON response with the appropriate status code and error message.


Redis Client Initialization
Description
The Redis client is initialized to manage token states, such as checking if a token is blacklisted.
Code Snippet
# Initialize the Redis client
redis_client = redis.StrictRedis(host="127.0.0.1", port=6379, decode_responses=True)
Parameters
•  host: The hostname of the Redis server (default is 127.0.0.1).
•  port: The port on which the Redis server is running (default is 6379).
•  decode_responses: If set to True, the Redis client will decode responses to strings.
Usage
•  The redis_client can be used throughout the application to interact with the Redis database, such as checking the status of tokens.
Conclusion
This module provides essential middleware for handling API key validation and token management in a FastAPI application. It ensures that only valid requests are processed and enhances the security of the application by adding necessary HTTP headers. The Redis client allows for efficient management of token states, contributing to the overall functionality of the application.


