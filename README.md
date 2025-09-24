# Password-Based Authentication System

This project was developed as part of my **Computer Security course**.  
It is a simple implementation of a password-based authentication system written in **C++**, with features to create accounts, sign in, and identify users securely.

---

## 🔐 Features
- **Password Hashing (DJB2 algorithm)** – adds security by storing only hashed passwords.
- **Salting** – unique salts are added to strengthen password storage.
- **Multithreaded programming** – supports handling multiple requests at once.
- **Semaphores & Mutex locks** – ensures safe access to the password file.
- **Account Management**:
  - Sign Up (create a new account with username and password)
  - Sign In (verify username and password)
  - Identification (find the account owner from a given password)

---

## 📂 Project Structure
- `README.md`
- `proII.cpp.txt` 
- `project2.exe` 
- `project2_CS.pdf` 
- `project2_code.txt` 
- `proll.cpp` 



## ⚙️ How It Works
1. **Sign Up** – creates a new account, generates salt, hashes password + salt, and stores it in the file.
2. **Sign In** – checks username and hashed password against the file.
3. **Identification** – takes a password and matches it to an existing account.

---

## 🛠️ Technologies Used
- **C++** (multithreading & file handling)
- **Semaphores and mutexes**
- **Hashing algorithm (DJB2)**

---

## 👨‍💻 Authors
- Eng. Ward Mohammad Ghnaim  
- Eng. Noor Aldden Shamroukh

---

## 📚 Course
Computer Security – The Hashemite University
