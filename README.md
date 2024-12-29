import hashlib
import os
import argparse
import json

# Storage file for user data
USER_DATA_FILE = "users.json"

def load_users():
    """Load users from the JSON file."""
    if not os.path.exists(USER_DATA_FILE):
        return {}
    with open(USER_DATA_FILE, 'r') as file:
        return json.load(file)

def save_users(users):
    """Save users to the JSON file."""
    with open(USER_DATA_FILE, 'w') as file:
        json.dump(users, file)

def hash_password(password):
    """Hash a password using SHA256."""
    return hashlib.sha256(password.encode()).hexdigest()

def register_user(username, email, password):
    """Register a new user."""
    users = load_users()

    if email in users:
        print("Error: Email is already registered.")
        return

    users[email] = {
        "username": username,
        "password": hash_password(password),
    }
    save_users(users)
    print("Registration successful!")

def login_user(email, password):
    """Authenticate a user."""
    users = load_users()

    if email not in users:
        print("Error: Email not found.")
        return

    if users[email]["password"] == hash_password(password):
        print(f"Welcome back, {users[email]['username']}!")
    else:
        print("Error: Incorrect password.")

def main():
    parser = argparse.ArgumentParser(description="User Registration and Authentication System")
    subparsers = parser.add_subparsers(dest="command")

    # Register command
    register_parser = subparsers.add_parser("register", help="Register a new user")
    register_parser.add_argument("username", type=str, help="Your username")
    register_parser.add_argument("email", type=str, help="Your email")
    register_parser.add_argument("password", type=str, help="Your password")

    # Login command
    login_parser = subparsers.add_parser("login", help="Login as an existing user")
    login_parser.add_argument("email", type=str, help="Your email")
    login_parser.add_argument("password", type=str, help="Your password")

    args = parser.parse_args()

    if args.command == "register":
        register_user(args.username, args.email, args.password)
    elif args.command == "login":
        login_user(args.email, args.password)
    else:
        parser.print_help()

if __name__ == "__main__":
    main()
