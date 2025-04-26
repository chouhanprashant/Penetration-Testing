# Insecure Direct Object Reference (IDOR) Testing Guide

## 1. What is IDOR?
Insecure Direct Object Reference (IDOR) is a vulnerability where an attacker can access or modify resources by manipulating input data such as URLs, cookies, or hidden form fields. This occurs when an application exposes internal implementation objects (like database records, files, or user data) directly to the user without proper access control or validation.

## 2. Step-by-Step Instructions for Testing IDOR

### A. Testing via URL
1. **Identify Object References in URL:**
   - Look for URLs that expose object identifiers (e.g., `/user/profile?id=123` or `/invoice?invoice_id=456`).
   - Examine URLs that contain parameters such as `id`, `uid`, `user_id`, `order_id`, etc.
   
2. **Manipulate Parameters:**
   - Change the object ID in the URL (e.g., `id=123` to `id=124` or `id=125`).
   - If you can access data or perform actions on other users' resources, this is an indication of an IDOR vulnerability.

3. **Observe Responses:**
   - Check if the application returns sensitive data (e.g., personal information, order details, etc.) after changing the object reference.
   - Test with multiple values and note any inconsistencies in access control enforcement.

4. **Common Issues:**
   - Predictable or sequential object IDs (e.g., `id=1`, `id=2`, etc.) that can be iterated over by an attacker.

### B. Testing via Cookies
1. **Capture Cookies:**
   - Use tools like Burp Suite, OWASP ZAP, or browser developer tools to capture cookies associated with the application.
   - Look for session or user-specific cookies like `user_id`, `session_id`, or `account_id`.

2. **Modify Cookie Values:**
   - Try changing the values of the cookies to those of other users. For example, change `user_id=123` to `user_id=124`.
   - Resend the request and observe if access is granted to unauthorized resources.

3. **Verify Impact:**
   - Check if the system allows you to access or modify another user’s data (e.g., profile, order, or invoice details).

4. **Common Issues:**
   - Insecure session management or improper validation of user IDs in cookies.

### C. Testing via Hidden Fields
1. **Inspect HTML Forms:**
   - Use browser developer tools (F12) to inspect hidden fields in HTML forms.
   - Look for hidden fields like `<input type="hidden" name="user_id" value="123">` or `<input type="hidden" name="order_id" value="456">`.

2. **Modify Hidden Field Values:**
   - Modify the value of hidden fields (e.g., change `user_id=123` to `user_id=124`).
   - Submit the form and observe if the server processes the request with unauthorized data.

3. **Test for Unauthorized Actions:**
   - Try performing actions that should be restricted, such as changing another user's profile or submitting orders under a different user ID.

4. **Common Issues:**
   - Lack of proper input validation or access control checks on hidden fields.

## 3. Common Places to Find IDOR Vulnerabilities
- **URLs:** Any URL containing query parameters like `id`, `uid`, `order_id`, or `invoice_id` should be scrutinized.
- **Cookies:** Look for cookies containing sensitive data such as session IDs, user identifiers, or resource references.
- **Hidden Form Fields:** Forms with hidden fields that reference user-specific data, such as `user_id` or `product_id`, can expose vulnerabilities.
- **API Endpoints:** Look for endpoints like `/api/user/123`, where the object reference (e.g., `123`) can be manipulated.
- **File Access:** URLs or endpoints that reference files by name or ID (e.g., `/file?id=123`) are prime candidates for IDOR testing.

## 4. Practical Testing Tips

### A. **Automate Testing Using Tools:**
   - Tools like Burp Suite or OWASP ZAP can intercept and modify requests and responses in real time. Use them to automate the process of modifying parameters like object IDs or cookies.
   - Use Postman or Swagger UI for API testing to directly manipulate parameters and examine responses.

### B. **Test with Multiple User Roles:**
   - Ensure that your tests cover multiple roles (e.g., admin, regular user, guest). IDOR vulnerabilities may only be exploitable by users with certain roles.

### C. **Test for Predictable Object Identifiers:**
   - Try sequential values (e.g., `123`, `124`, `125`) to check if the IDs are easily guessable. A predictable pattern can be a sign of an IDOR vulnerability.
   
### D. **Look for Weak Authentication and Session Management:**
   - Insecure authentication systems can allow attackers to impersonate other users, especially when combined with IDOR. Always check if an attacker can change the user session or identity via cookies or parameters.

### E. **Try Blind IDOR:**
   - If the response does not explicitly reveal sensitive data, test for "blind IDOR" vulnerabilities by checking for subtle differences in responses (e.g., changes in response time or error messages) when manipulating parameters.

## 5. Quick Testing Checklist
- [ ] **Identify Object References:** Look for object IDs in URLs, cookies, hidden fields, and API endpoints.
- [ ] **Manipulate Parameters:** Test for access by modifying object references (e.g., `id=123` to `id=124`).
- [ ] **Test for Predictable IDs:** Check if the object IDs are sequential, sequentially guessable, or otherwise predictable.
- [ ] **Modify Cookies and Hidden Fields:** Test for session or user-specific information in cookies and hidden form fields.
- [ ] **Verify Access Control:** Ensure that the application enforces proper access control before allowing access to sensitive resources.
- [ ] **Check Multiple Roles:** Test for vulnerabilities across different user roles and permissions.
- [ ] **Observe Responses:** Look for any response discrepancies (e.g., unauthorized data exposure or actions).
- [ ] **Use Automated Tools:** Leverage tools like Burp Suite or Postman to streamline and automate testing.

**Note:** Always ensure you have permission to test and follow ethical guidelines when performing IDOR testing.
