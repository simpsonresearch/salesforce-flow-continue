# Salesforce Flow Continuation Architecture

## Overview
Secure flow resumption system allowing users to continue multi-step Salesforce flows via email verification with JWT tokens.

## Architecture Components

```
┌─────────────────┐    ┌─────────────────┐    ┌─────────────────┐
│   Salesforce    │    │   Azure API     │    │   User Email    │
│  Digital Exp.   │    │    Service      │    │   + Browser     │
└─────────────────┘    └─────────────────┘    └─────────────────┘
```

## Process Flow

### **New User Flow**
1. User enters name/email on Screen 1
2. Check if Lead exists with `Flow_Interview_ID__c`
3. **If NO**: Create Lead + assign Interview ID → Continue to Screen 2

### **Returning User Flow**
1. User enters name/email on Screen 1  
2. **If Lead EXISTS**: Show "Continue previous application?" button
3. User clicks "Continue" button
4. **Salesforce → Azure API**: POST `/continue-flow`
   ```json
   {
     "email": "user@email.com",
     "interviewId": "flow-guid-123",
     "firstName": "John",
     "orgId": "salesforce-org-id"
   }
   ```
5. **Azure API**:
   - Generates secure JWT token (1hr expiry)
   - Sends email via Microsoft Graph API
6. **User**: Clicks email link with JWT token
7. **Browser → Azure API**: POST `/validate-token`
8. **If Valid**: Redirect to Salesforce flow with Interview ID
9. **Salesforce**: Resume flow from saved step

## Security Features

🔒 **JWT Token Security**
- Secret stored securely in Azure (not exposed to Salesforce)
- 1-hour expiration
- Email verification required
- Org-specific validation

## Technical Stack

- **Salesforce**: Flow + Apex HTTP callouts
- **Azure**: App Service + Microsoft Graph (email)
- **Security**: JWT tokens + Azure Key Vault
- **Monitoring**: Azure Application Insights

## Key Benefits

✅ **Secure**: No secrets exposed to frontend  
✅ **User-Friendly**: Email-based continuation  
✅ **Enterprise**: Azure reliability & monitoring  
✅ **Scalable**: Stateless JWT validation  

---

**Result**: Users can securely resume Salesforce flows from any device by clicking a time-limited email link, with enterprise-grade security throughout the process.
