## Project Demo Video

[Click here to view the video](https://github.com/your-username/your-repo-name/blob/main/mergedCvideo.mp4)

CODE:
Vulnerable Code (Before Fix):
import sqlite3

# Connect to database
conn = sqlite3.connect("users.db")
cursor = conn.cursor()

# Create table
cursor.execute("CREATE TABLE IF NOT EXISTS users (username TEXT, password TEXT)")

# Insert sample user (hardcoded password)
cursor.execute("INSERT INTO users VALUES ('admin', 'admin123')")
conn.commit()

print("=== Login System ===")
username = input("Enter username: ")
password = input("Enter password: ")

# SQL Injection Vulnerability
query = f"SELECT * FROM users WHERE username = '{username}' AND password = '{password}'"
cursor.execute(query)

result = cursor.fetchone()

if result:
    print("Login Successful!")
else:
    print("Login Failed!")

conn.close()

Secure Version (After Fix):
import sqlite3
import hashlib

def hash_password(password):
    return hashlib.sha256(password.encode()).hexdigest()

conn = sqlite3.connect("users.db")
cursor = conn.cursor()

cursor.execute("CREATE TABLE IF NOT EXISTS users (username TEXT, password TEXT)")

# Insert hashed password
hashed_password = hash_password("admin123")
cursor.execute("INSERT OR IGNORE INTO users VALUES (?, ?)", ("admin", hashed_password))
conn.commit()

print("=== Secure Login System ===")
username = input("Enter username: ").strip()
password = input("Enter password: ").strip()

hashed_input_password = hash_password(password)

# Prevent SQL Injection using parameterized query
cursor.execute("SELECT * FROM users WHERE username = ? AND password = ?", 
               (username, hashed_input_password))

result = cursor.fetchone()

if result:
    print("Login Successful!")
else:
    print("Login Failed!")

conn.close()


