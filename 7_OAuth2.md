# **OAuth 2.0**  

## **1. What is OAuth 2.0?**  
OAuth 2.0 (Open Authorization) is an **industry-standard protocol for authorization**, allowing applications to **securely access resources on behalf of a user** without exposing credentials. It enables **secure delegation of access** between clients, resource owners, and authorization servers.

🔹 **Authentication vs Authorization in OAuth 2.0:**  
- **Authentication** → Verifying **who** the user is (handled by OpenID Connect if needed).  
- **Authorization** → Determining **what** the user can do (handled by OAuth 2.0).  

OAuth 2.0 is widely used in **cloud applications, APIs, mobile apps, and third-party integrations** (e.g., logging in with Google, Facebook, GitHub).

---

## **2. Key Components of OAuth 2.0**  
| **Component** | **Description** |
|--------------|----------------|
| **Resource Owner** | The user who owns the protected resource. |
| **Client** | The application requesting access (e.g., a mobile app, web app, service). |
| **Authorization Server** | Issues tokens after verifying the user’s identity (e.g., Google, Okta, Auth0). |
| **Resource Server** | The API that holds protected resources and enforces token validation. |
| **Access Token** | A short-lived token that allows the client to access protected resources. |
| **Refresh Token** | A long-lived token used to obtain new access tokens when they expire. |

---

## **3. OAuth 2.0 Grant Types (Flows)**
OAuth 2.0 provides **multiple flows (grant types)** depending on the application type and security requirements.

## **A. Authorization Code Flow (Most Secure, Used for Web Apps)**
Used for **web and mobile applications** where a user logs in via a third-party service (e.g., Google, Facebook).

🔹 **How it Works:**  
1. The **user is redirected** to the authorization server (e.g., Google login page).  
2. The user **grants consent** to allow the client application to access resources.  
3. The authorization server **issues an authorization code** (temporary).  
4. The client exchanges the **authorization code for an access token**.  
5. The client **uses the access token to access protected APIs**.  

🔹 **Example (Google OAuth 2.0 Login Flow)**  
```plaintext
1. User clicks "Login with Google"
2. Redirects to: https://accounts.google.com/o/oauth2/auth
3. User consents → Google issues an authorization code.
4. Client exchanges code at: https://oauth2.googleapis.com/token
5. Google returns access token → Client accesses Google API.
```
✅ **Secure** because **tokens are not exposed in URLs**.  
✅ **Refresh Token Support** allows token renewal without user interaction.

---

## **B. Client Credentials Flow (Machine-to-Machine)**
Used for **server-to-server authentication** (e.g., microservices, backend applications).

🔹 **How it Works:**  
1. The **client requests an access token** from the authorization server using client credentials.  
2. The **server verifies** the client credentials and issues an access token.  
3. The client **uses the token to call the API**.  

🔹 **Example (Getting an Access Token in Backend)**  
```http
POST /token HTTP/1.1
Host: auth.example.com
Content-Type: application/x-www-form-urlencoded

grant_type=client_credentials
&client_id=CLIENT_ID
&client_secret=CLIENT_SECRET
```
✅ **Secure** for machine-to-machine API communication.  
❌ **No user context** → Only suitable for system-level access.

---

## **C. Implicit Flow (Deprecated, Used for SPAs)**
Previously used for **single-page applications (SPAs)** where tokens were sent directly in the browser URL.

🔹 **Why It's Deprecated?**  
- **Security risks** (tokens in URLs are exposed in logs and referrers).  
- **Lack of refresh tokens** (users must log in again when tokens expire).  

✅ **Use Authorization Code Flow with PKCE Instead** (for SPAs).  

---

## **D. Password Grant (Deprecated, Direct Login with Credentials)**
The client directly collects the **username and password** and exchanges them for an access token.

🔹 **Why It's Insecure?**  
- The client **must store passwords**, violating OAuth’s purpose.  
- **Phishing risk** if users enter credentials in malicious apps.  

✅ **Use Authorization Code Flow Instead**.

---

## **4. Tokens in OAuth 2.0**
OAuth 2.0 issues **two key tokens**:  

| **Token Type** | **Purpose** | **Lifespan** |
|---------------|------------|-------------|
| **Access Token** | Grants access to resources (APIs) | Short-lived (e.g., 1 hour) |
| **Refresh Token** | Used to get a new access token | Long-lived (e.g., weeks/months) |

🔹 **Example Access Token (JWT Format)**
```json
{
  "alg": "RS256",
  "typ": "JWT"
}
.
{
  "sub": "1234567890",
  "name": "John Doe",
  "iat": 1615314210,
  "exp": 1615317810,
  "scope": "read write"
}
.
(signature)
```
✅ **JWT (JSON Web Token) Format** allows stateless verification.  
✅ **Expirable tokens** reduce security risks.  

---

## **5. OAuth 2.0 Security Best Practices**
✅ **Use Authorization Code Flow with PKCE** for web & mobile apps.  
✅ **Never store access tokens in localStorage or URLs** (use HTTP-only cookies).  
✅ **Use short-lived access tokens with refresh tokens**.  
✅ **Enforce token expiration and revocation**.  
✅ **Use mTLS or JWT validation for secure token exchange**.  

---

## **6. OAuth 2.0 in Action (Example with Google OAuth)**
## **Step 1: Redirect User to Google Authorization URL**
```plaintext
https://accounts.google.com/o/oauth2/auth?
    client_id=CLIENT_ID
    &redirect_uri=https://yourapp.com/callback
    &response_type=code
    &scope=email profile
    &state=secure_random_string
```

## **Step 2: Exchange Authorization Code for Access Token**
```http
POST /token HTTP/1.1
Host: oauth2.googleapis.com
Content-Type: application/x-www-form-urlencoded

grant_type=authorization_code
&code=AUTHORIZATION_CODE
&redirect_uri=https://yourapp.com/callback
&client_id=CLIENT_ID
&client_secret=CLIENT_SECRET
```

## **Step 3: Use Access Token to Access Google APIs**
```http
GET /userinfo HTTP/1.1
Host: www.googleapis.com
Authorization: Bearer ACCESS_TOKEN
```
✅ **Returns user profile data** → Your app can now display user info.

---

## **7. OAuth 2.0 vs Other Access Models**
| **Feature** | **OAuth 2.0** | **RBAC** | **ABAC** | **PBAC** |
|------------|--------------|---------|---------|---------|
| **Best For** | API & Web Authentication | Enterprise Roles | Dynamic, Context-Based Control | Policy-Driven Access |
| **User Authentication** | Yes (via OpenID Connect) | No | No | No |
| **Machine Authentication** | Yes | No | No | No |
| **Context Awareness** | Medium | Low | High | High |

---

## **8. OAuth 2.0 Use Cases**
| **Industry** | **Use Case** |
|-------------|-------------|
| **Social Media Logins** | "Login with Google, Facebook, GitHub" |
| **Cloud Security** | AWS IAM, Google Cloud OAuth authentication |
| **API Authorization** | Accessing protected APIs with tokens |
| **Single Sign-On (SSO)** | Secure enterprise authentication |

---

## **9. Key Takeaways**
- **OAuth 2.0 is the most widely used authorization framework for web & API security.**  
- **Grants secure access without exposing user credentials.**  
- **Uses access tokens & refresh tokens for secure session management.**  
- **Authorization Code Flow with PKCE is the recommended approach.**  
- **Critical for API security, cloud computing, and federated identity systems.**  

---