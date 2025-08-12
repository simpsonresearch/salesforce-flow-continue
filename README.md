# Salesforce Flow Continuation Architecture

Secure flow resumption system allowing users to continue multi-step Salesforce flows via email verification with JWT tokens.

## Process Flow

### **Flow A: New User**
1. User enters name/email on Screen 1
2. Check if a Lead with the provided email already exists
3. If the lead doesn't exist, create a lead, then continue to the next screen.

### **Flow B: Returning User**
1. User enters name/email on Screen 1  
2. If the lead exists, show a "Continue previous application?" popup and button
3. If the user clicks the "Continue" button, send a POST API request to `/continue-flow?email="user@email.com"`
5. In the API backend:
   - Generates secure JWT token (1hr expiry)
   - Sends email via Microsoft Graph API
6. User then clicks email link with JWT token → Navigates to link in browser
7. Check if `token` param exists in the URL. If it does, execute the following steps:
   - Grab token from URL params, then send POST API request to `/validate-token?token="..."`
   - If the token is valid, fetch lead using email and resume flow from saved step (in lead object)

## Security Features

🔒 **JWT Token Security**
- Secret stored securely in Azure (not exposed to Salesforce)
- 1-hour expiration
- Email verification required

## Tech Stack

- **Salesforce**: Flow + Apex HTTP callouts
- **Azure**: Express API + Microsoft Graph (email)
- **Security**: JWT tokens + Azure Key Vault
