# Insecure Direct Object Reference (IDOR) Testing Guide

## 1. What is IDOR?
Insecure Direct Object Reference (IDOR) is a security vulnerability that occurs when an attacker can manipulate input to access or modify resources that they shouldn't have access to. This typically happens when an application directly references internal objects, such as files, database records, or other resources, based on user input without adequate access control.

### Example:
For instance, if a URL looks like `/profile?id=123`, an attacker might change `id=123` to `id=124` to access another user's profile. Proper authorization checks should ensure that only authorized users can access or modify their resources.

---

## 2. Step-by-Step Instructions for Testing IDOR

### A. Testing via URL Parameters
1. **Identify Parameters:**
   - Start by identifying URLs that contain parameters that reference objects (e.g., `/user/profile?id=123`, `/order/view?order_id=456`, `/file/download?file_id=789`).
   - Look for parameters such as `id`, `user_id`, `file_id`, `order_id`, etc.

2. **Test Parameter Manipulation:**
   - Modify the parameter value (e.g., change `id=123` to `id=124` or increment it). In some cases, IDs might follow a predictable pattern.
   - Test for various edge cases: sequential IDs, random IDs, or very large/small numbers.

3. **Observe Responses:**
   - If the system grants access to unauthorized data or performs unauthorized actions (e.g., viewing another user’s profile, deleting a record), then you’ve discovered an IDOR vulnerability.
   - Look for subtle clues, like redirected URLs or HTTP error codes indicating unauthorized access.

4. **Example Test Case:**
   - **URL:** `https://example.com/order/view?order_id=1001`
   - Modify the parameter `order_id=1001` to `order_id=1002`, `1003`, etc., and check if you can access orders belonging to other users.

---

### B. Testing via Cookies
1. **Capture Cookies:**
   - Use a web proxy tool like Burp Suite or OWASP ZAP, or browser developer tools, to intercept and capture the cookies that the application sets.
   - Look for session-related cookies or any cookies that might contain user identifiers (e.g., `session_id`, `user_id`, `account_id`).

2. **Modify Cookie Values:**
   - Once you identify a cookie with a user-specific identifier, modify its value (e.g., change `user_id=123` to `user_id=124`).
   - Send the request again and check if you can access another user’s resources or perform actions on their behalf.

3. **Verify the Impact:**
   - If modifying the cookie grants access to unauthorized data (e.g., access to another user’s account settings or resources), the system may be vulnerable to IDOR.

---

### C. Testing via Hidden Fields
1. **Inspect Hidden Fields:**
   - Use browser developer tools (F12) to inspect hidden form fields within HTML forms.
   - Look for fields with attributes like `<input type="hidden" name="user_id" value="123">`, `<input type="hidden" name="order_id" value="456">`, or similar fields that might contain object references.

2. **Modify Hidden Field Values:**
   - Change the value of these hidden fields (e.g., change `user_id=123` to `user_id=124`).
   - Submit the form and observe if the application accepts the modified input.

3. **Test for Unauthorized Access:**
   - Try to perform actions such as updating another user's profile, deleting records, or modifying resources.
   - If you can modify or access unauthorized data by manipulating hidden fields, then the application is vulnerable to IDOR.

---

### D. Testing via API Endpoints
1. **Identify API Endpoints:**
   - Look for API endpoints that expose user or resource-specific identifiers in the URL path (e.g., `/api/user/123`, `/api/order/456`, `/api/file/789`).
   - Pay attention to GET, POST, PUT, DELETE requests that reference object IDs.

2. **Test with Different IDs:**
   - Modify the object reference in the API request to test for unauthorized access or modification of resources (e.g., change `/api/user/123` to `/api/user/124`).
   - Use tools like Postman or Swagger UI to make these requests and observe the response.

3. **Check Response Codes:**
   - If the response allows you to perform actions or return data from other users or objects, this indicates an IDOR vulnerability.
   - Check for HTTP status codes like `200 OK`, `404 Not Found`, or `403 Forbidden` to determine if access control is being enforced correctly.

---

## 3. Common Places to Find IDOR Vulnerabilities

- **User Profiles:** URLs like `/profile?id=123`, `/account/settings?user_id=456`, etc.
- **E-commerce Platforms:** Resources such as orders, shopping carts, and invoices (e.g., `/order/view?id=123`, `/invoice?id=789`).
- **File Download/Upload:** URLs or endpoints that expose file IDs (e.g., `/file/download?file_id=123`).
- **API Endpoints:** API endpoints that expose user IDs or object references in the URL or request body (e.g., `/api/user/{id}`, `/api/order/{order_id}`).
- **Admin Interfaces:** Look for administrative interfaces that may inadvertently expose data belonging to other users (e.g., `/admin/user/edit?user_id=123`).

---

## 4. Practical Testing Tips

### A. Use Automation Tools
- **Burp Suite:** Intercept and modify requests in real-time, automate testing by setting up a Repeater or Intruder attack to change parameters in URLs, cookies, and hidden fields.
- **OWASP ZAP:** Similarly, use ZAP's active scanner and manual testing features to manipulate parameters and analyze responses.

### B. Test Across Multiple Roles
- Check for IDOR vulnerabilities by logging in as users with different roles (e.g., admin, regular user, guest) and attempt to access resources not meant for that role.
- Often, IDOR is more easily exploited by users with limited access because they have fewer objects to interact with.

### C. Handle Blind IDOR
- **Blind IDOR:** Sometimes, you may not see a direct response revealing unauthorized data. Look for indirect indicators like HTTP response time differences or error messages (e.g., `403 Forbidden` when accessing a resource you shouldn’t).
- Use a proxy to track changes in response times when modifying object IDs. Anomalies can hint at IDOR vulnerabilities.

### D. Check for Session Management Flaws
- If the application relies heavily on session cookies, check if session IDs or user IDs are predictable or can be modified to impersonate another user.

### E. Use Guessing for Sequential IDs
- If object IDs are predictable or sequential, a simple brute-force approach could be used to find other valid IDs. This method is particularly useful for testing APIs, URLs, or files with incremental object references.

---

## 5. Quick Testing Checklist

- [ ] **Identify Object References:** Look for parameters in URLs, cookies, hidden fields, or API endpoints that could reference sensitive data or resources.
- [ ] **Manipulate Parameters:** Test if modifying parameters like `user_id`, `order_id`, `file_id`, etc., allows unauthorized access to data or actions.
- [ ] **Test for Sequential/Guessable IDs:** Check for predictable or sequential object identifiers that could be easily manipulated.
- [ ] **Verify Access Control:** Ensure that only authorized users can access or modify resources related to their session or permissions.
- [ ] **Test Multiple Roles:** Attempt accessing resources from different user roles to test for access control flaws.
- [ ] **Use Automated Tools:** Leverage tools like Burp Suite or OWASP ZAP to automate the identification and exploitation of IDOR vulnerabilities.
- [ ] **Observe Subtle Differences:** For blind IDOR, look for changes in response behavior, such as HTTP status codes or response times, to identify vulnerabilities.
- [ ] **Check API Responses:** Ensure that API responses properly filter out unauthorized users from accessing or modifying data.

**Note:** Always ensure that you have explicit permission to perform security testing on the target systems. Unauthorized testing can have legal consequences.

---

