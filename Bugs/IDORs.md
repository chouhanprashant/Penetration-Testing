═════════════════════════════════════════════════════════════
IDOR (Insecure Direct Object Reference) - Practical Guide
═════════════════════════════════════════════════════════════

🛡️ What is IDOR?
- Accessing or modifying data by changing IDs in the URL, cookies, or hidden fields, without proper authorization checks.

═════════════════════════════════════════════════════════════
🧪 How to Test for IDOR (Step-by-Step):

1. Create two accounts (both your own).
2. Perform an action (buy, upload, view something) from one account.
3. Capture the request (Burp Suite or Inspect Element).
4. Look for:
   - URL parameters (e.g., id=1234)
   - Cookies (e.g., user_id=1234)
   - Hidden input fields (e.g., <input type="hidden" name="admin" value="false">)

5. Change the ID or value:
   - Try your second account's ID.
   - Try admin ID (example: 0 or 1).
   - Change hidden field values (false ➔ true).

6. Observe if you get unauthorized access.

═════════════════════════════════════════════════════════════
🔍 Common Places to Find IDOR:

- URL:
  Example ➔ https://website.com/invoice?id=12
  ➔ Try changing id=12 ➔ id=1

- Cookies:
  Example ➔ user_id=1234
  ➔ Try changing user_id=0 or another user's ID.

- Hidden Fields:
  Example ➔ 
  <input type="hidden" name="admin" value="false">
  ➔ Change value ➔ true

- Uploaded Files:
  Example ➔ https://website.com/image?id=089
  ➔ Change id=089 ➔ id=086

═════════════════════════════════════════════════════════════
⚡ Practical Tips:

- Always use YOUR OWN second account when testing.
- Use Burp Suite to capture and modify requests easily.
- Always test hidden fields, cookies, URL parameters.
- After purchasing, uploading, or logging in, try changing IDs.
- NEVER test against data/accounts you do not own without permission.

═════════════════════════════════════════════════════════════
🚀 Quick Copy Testing Checklist:

✅ Two personal accounts created.
✅ Capture request after an action.
✅ Modify IDs in URL, Cookies, Hidden Fields.
✅ Check if unauthorized access happens.
✅ Log findings carefully.

═════════════════════════════════════════════════════════════
