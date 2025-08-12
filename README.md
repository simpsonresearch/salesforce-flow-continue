# Salesforce Flow Continuation Architecture

Secure flow resumption system allowing users to continue multi-step Salesforce flows via email verification with JWT tokens.

## Process Flow

### **New User Flow**
1. User enters name/email on Screen 1
2. Check if a Lead with the provided email already exists
3. **If NO**: Create Lead → Continue to Screen 2

### **Returning User Flow**
1. User enters name/email on Screen 1  
2. **If Lead EXISTS**: Show "Continue previous application?" popup and button
3. User clicks the "Continue" button
4. **Salesforce → Azure API**: POST `/continue-flow`
   ```json
   {
     "email": "user@email.com"
   }
   ```
5. **Azure API**:
   - Generates secure JWT token (1hr expiry)
   - Sends email via Microsoft Graph API
6. **User**: Clicks email link with JWT token
7. **Browser → Azure API**: POST `/validate-token`
8. **If Valid**: Fetch lead using email and resume flow from saved step (in lead object)

## Security Features

🔒 **JWT Token Security**
- Secret stored securely in Azure (not exposed to Salesforce)
- 1-hour expiration
- Email verification required

## Tech Stack

- **Salesforce**: Flow + Apex HTTP callouts
- **Azure**: App Service + Microsoft Graph (email)
- **Security**: JWT tokens + Azure Key Vault

## Key Benefits

✅ **Secure**: No secrets exposed to frontend  
✅ **User-Friendly**: Email-based continuation  
✅ **Enterprise**: Azure reliability & monitoring  
✅ **Scalable**: Stateless JWT validation  
